---
uid: 1ea43878-a7a1-4e48-afa7-5ed40619b52e
slug: retry
title: "Thử lại có giới hạn"
estimatedMinutes: 35
objectives:
  - "Viết hàm gửi có thử lại tối đa 3 lần"
  - "Chờ theo retry_after khi gặp 429, chờ tăng dần khi gặp 5xx"
  - "Dừng ngay với lỗi 4xx khác 429 và in log dễ đọc"
authors: [github:TienNHM]
---

# Thử lại có giới hạn

Nâng cấp script của chương 4: thay vì gửi một lần, hàm `send` thử tối đa 3 lần theo quy tắc của bài trước.

| Gặp | Làm gì |
| --- | --- |
| 2xx | Xong |
| 429 | Chờ `retry_after` giây, thử lại |
| 5xx, mất mạng | Chờ 2, rồi 4 giây (tăng gấp đôi), thử lại |
| 4xx khác | Dừng, in lỗi |
| Hết 3 lần | Dừng, thoát với mã lỗi 1 |

Chọn bản Python hoặc Node.js giống chương 4.

## Python

```python
import json
import os
import sys
import time
import urllib.error
import urllib.request

MAX_ATTEMPTS = 3


def send(url, payload):
    """Gửi payload, thử lại khi gặp 429 hoặc lỗi 5xx. Trả về True nếu gửi được."""
    body = json.dumps(payload).encode("utf-8")
    for attempt in range(1, MAX_ATTEMPTS + 1):
        request = urllib.request.Request(
            url,
            data=body,
            headers={"Content-Type": "application/json", "User-Agent": "dsc-webhook-lesson/1.0"},
            method="POST",
        )
        try:
            with urllib.request.urlopen(request, timeout=10) as response:
                print(f"[lần {attempt}] Đã gửi, status {response.status}")
                return True
        except urllib.error.HTTPError as error:
            text = error.read().decode("utf-8")
            if error.code == 429:
                wait = json.loads(text).get("retry_after", 1)
                print(f"[lần {attempt}] 429 rate limit")
            elif error.code >= 500:
                wait = 2 ** attempt
                print(f"[lần {attempt}] Lỗi server {error.code}")
            else:
                # 400, 401, 404...: gửi lại y hệt cũng sẽ lỗi, nên dừng ngay.
                print(f"[lần {attempt}] Lỗi {error.code}: {text}")
                return False
        except urllib.error.URLError as error:
            wait = 2 ** attempt
            print(f"[lần {attempt}] Không kết nối được ({error.reason})")
        if attempt < MAX_ATTEMPTS:
            print(f"        chờ {wait} giây rồi thử lại")
            time.sleep(wait)
    print(f"Bỏ cuộc sau {MAX_ATTEMPTS} lần")
    return False


url = os.environ.get("WEBHOOK_URL")
if not url:
    sys.exit("Chưa đặt biến môi trường WEBHOOK_URL")
text = " ".join(sys.argv[1:]) or "Xin chào!"
ok = send(url, {"content": text, "allowed_mentions": {"parse": []}})
sys.exit(0 if ok else 1)
```

## Node.js

```js
const MAX_ATTEMPTS = 3;
const sleep = (seconds) => new Promise((resolve) => setTimeout(resolve, seconds * 1000));

/** Gửi payload, thử lại khi gặp 429 hoặc lỗi 5xx. Trả về true nếu gửi được. */
async function send(url, payload) {
  for (let attempt = 1; attempt <= MAX_ATTEMPTS; attempt++) {
    let wait;
    try {
      const response = await fetch(url, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload),
        signal: AbortSignal.timeout(10_000),
      });
      if (response.ok) {
        console.log(`[lần ${attempt}] Đã gửi, status ${response.status}`);
        return true;
      }
      const text = await response.text();
      if (response.status === 429) {
        wait = JSON.parse(text).retry_after ?? 1;
        console.log(`[lần ${attempt}] 429 rate limit`);
      } else if (response.status >= 500) {
        wait = 2 ** attempt;
        console.log(`[lần ${attempt}] Lỗi server ${response.status}`);
      } else {
        // 400, 401, 404...: gửi lại y hệt cũng sẽ lỗi, nên dừng ngay.
        console.log(`[lần ${attempt}] Lỗi ${response.status}: ${text}`);
        return false;
      }
    } catch (error) {
      wait = 2 ** attempt;
      console.log(`[lần ${attempt}] Không kết nối được (${error.message})`);
    }
    if (attempt < MAX_ATTEMPTS) {
      console.log(`        chờ ${wait} giây rồi thử lại`);
      await sleep(wait);
    }
  }
  console.log(`Bỏ cuộc sau ${MAX_ATTEMPTS} lần`);
  return false;
}

const url = process.env.WEBHOOK_URL;
if (!url) {
  console.error("Chưa đặt biến môi trường WEBHOOK_URL");
  process.exit(1);
}
const text = process.argv.slice(2).join(" ") || "Xin chào!";
const ok = await send(url, { content: text, allowed_mentions: { parse: [] } });
process.exit(ok ? 0 : 1);
```

## Đọc log

Chạy với một webhook đang bị rate limit sẽ in ra kiểu:

```text
[lần 1] 429 rate limit
        chờ 0.512 giây rồi thử lại
[lần 2] Đã gửi, status 204
```

Mỗi dòng có **số lần thử**, **chuyện gì xảy ra** và **sẽ làm gì tiếp**. Khi workflow lỗi lúc 3 giờ sáng, log như vậy giúp bạn hiểu ngay mà không phải chạy lại.

Mã thoát (`exit 1` khi thất bại) cũng quan trọng: GitHub Actions dựa vào nó để đánh dấu bước bị đỏ, và job `if: failure()` ở chương 6 mới chạy.

## Gửi nhiều tin liền nhau

Cần gửi nhiều tin (ví dụ 20 bài RSS mới)? Gộp lại thay vì gửi từng tin: một tin có tới **10 embed**. 20 bài là 2 tin, không phải 20.

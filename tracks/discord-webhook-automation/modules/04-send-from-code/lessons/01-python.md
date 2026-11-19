---
uid: 5d851641-8d1c-4c50-b562-05d58c9a376f
slug: python
title: "Gửi bằng Python"
estimatedMinutes: 30
objectives:
  - "Gửi tin nhắn webhook bằng thư viện chuẩn urllib, không cài thêm gì"
  - "Đọc webhook URL từ biến môi trường và dừng có thông báo khi thiếu"
  - "Xử lý lỗi HTTP và lỗi mạng thay vì để script văng traceback"
authors: [github:TienNHM]
---

# Gửi bằng Python

Chọn **một** trong hai bài của chương này: Python hoặc Node.js. Cả hai làm cùng một việc.

## Script

Tạo file `send.py`:

```python
import json
import os
import sys
import urllib.error
import urllib.request

url = os.environ.get("WEBHOOK_URL")
if not url:
    sys.exit("Chưa đặt biến môi trường WEBHOOK_URL")

text = " ".join(sys.argv[1:]) or "Xin chào từ Python!"
payload = {"content": text, "allowed_mentions": {"parse": []}}

request = urllib.request.Request(
    url,
    data=json.dumps(payload).encode("utf-8"),
    headers={
        "Content-Type": "application/json",
        # Không đặt User-Agent thì Discord có thể chặn request (403).
        "User-Agent": "dsc-webhook-lesson/1.0",
    },
    method="POST",
)

try:
    with urllib.request.urlopen(request, timeout=10) as response:
        print("Đã gửi, status", response.status)
except urllib.error.HTTPError as error:
    print("Discord trả lỗi", error.code, error.read().decode("utf-8"))
    sys.exit(1)
except urllib.error.URLError as error:
    print("Không kết nối được:", error.reason)
    sys.exit(1)
```

Những điểm đáng chú ý:

- `os.environ.get("WEBHOOK_URL")`: URL nằm ngoài code, nên commit code lên GitHub vẫn an toàn.
- `json.dumps(payload)`: để Python tạo JSON, không tự ghép chuỗi. Tự ghép thì một dấu `"` trong nội dung là JSON hỏng.
- `User-Agent`: thư viện `urllib` gửi tên mặc định `Python-urllib/3.x`, và Discord có thể chặn tên này bằng lỗi `403`. Đặt một tên riêng là đủ.
- `timeout=10`: mạng treo thì script dừng sau 10 giây thay vì chờ mãi.

## Chạy

Đặt `WEBHOOK_URL` như chương 2 (trong cùng terminal), rồi:

```bash
python send.py "Tin nhắn từ Python"
```

Kết quả: `Đã gửi, status 204`. Bỏ trống phần chữ thì script gửi câu mặc định.

## Thử các lỗi

- Mở terminal mới (chưa có `WEBHOOK_URL`) và chạy lại: script dừng với `Chưa đặt biến môi trường WEBHOOK_URL`.
- Đặt `WEBHOOK_URL` thành URL cũ đã xóa webhook: `Discord trả lỗi 404 {"message": "Unknown Webhook", ...}`.

## Gửi embed

Thay `payload` bằng một `dict` có `embeds` như chương 3. Python dùng `True`/`False`/`None`; `json.dumps` tự đổi thành `true`/`false`/`null`.

Thư viện [requests](https://requests.readthedocs.io/) viết gọn hơn (`requests.post(url, json=payload)`), nhưng cần `pip install requests`. Bài này dùng `urllib` để chạy được ngay.

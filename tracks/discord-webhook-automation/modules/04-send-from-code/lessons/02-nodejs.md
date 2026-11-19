---
uid: 943a5f44-89ea-4902-8a30-336dbd5bafde
slug: nodejs
title: "Gửi bằng Node.js"
estimatedMinutes: 30
objectives:
  - "Gửi tin nhắn webhook bằng fetch có sẵn trong Node.js"
  - "Nạp WEBHOOK_URL từ file .env bằng node --env-file"
  - "Xử lý response không thành công và lỗi mạng"
authors: [github:TienNHM]
---

# Gửi bằng Node.js

Chọn **một** trong hai bài của chương này: Python hoặc Node.js. Cả hai làm cùng một việc.

## Script

Tạo file `send.mjs` (đuôi `.mjs` để dùng được `await` ở ngoài hàm):

```js
const url = process.env.WEBHOOK_URL;
if (!url) {
  console.error("Chưa đặt biến môi trường WEBHOOK_URL");
  process.exit(1);
}

const text = process.argv.slice(2).join(" ") || "Xin chào từ Node.js!";
const payload = { content: text, allowed_mentions: { parse: [] } };

try {
  const response = await fetch(url, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload),
    signal: AbortSignal.timeout(10_000),
  });
  if (!response.ok) {
    console.error("Discord trả lỗi", response.status, await response.text());
    process.exit(1);
  }
  console.log("Đã gửi, status", response.status);
} catch (error) {
  console.error("Không kết nối được:", error.message);
  process.exit(1);
}
```

- `fetch` có sẵn từ Node 18, không cần cài gì.
- `JSON.stringify(payload)`: để Node tạo JSON, không tự ghép chuỗi.
- `response.ok` là `true` khi status trong khoảng 200–299. `fetch` **không** báo lỗi khi gặp 400 hay 404, nên phải tự kiểm tra.
- `AbortSignal.timeout(10_000)`: dừng sau 10 giây nếu mạng treo.

## Chạy với file .env

Thay vì đặt biến môi trường mỗi lần mở terminal, tạo file `.env` cạnh script:

```text
WEBHOOK_URL=https://discord.com/api/webhooks/<id>/<token>
```

Rồi chạy:

```bash
node --env-file=.env send.mjs "Tin nhắn từ Node.js"
```

Kết quả: `Đã gửi, status 204`.

**Trước khi `git add`**, tạo file `.gitignore` có dòng:

```text
.env
```

rồi chạy `git status`: `.env` không được xuất hiện trong danh sách. Chương 5 nói kỹ hơn.

## Thử các lỗi

- Chạy `node send.mjs` không có `--env-file`: script dừng với `Chưa đặt biến môi trường WEBHOOK_URL`.
- Sửa một ký tự trong token ở `.env`: `Discord trả lỗi 401` hoặc `404`.

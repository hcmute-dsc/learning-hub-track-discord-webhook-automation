---
uid: 4cc76c01-8fbf-4dad-b882-f3c9b2b723bc
slug: send-with-curl
title: "Gửi tin nhắn đầu tiên bằng curl"
estimatedMinutes: 25
objectives:
  - "Lưu webhook URL vào biến môi trường thay vì gõ thẳng vào lệnh"
  - "Gửi tin nhắn bằng curl từ một file JSON"
  - "Đọc kết quả 204, 200 và các lỗi 4xx thường gặp"
authors: [github:TienNHM]
---

# Gửi tin nhắn đầu tiên bằng curl

## 1. Lưu URL vào biến môi trường

Để URL không nằm trong lịch sử lệnh hay ảnh chụp màn hình, lưu nó vào biến `WEBHOOK_URL` trong terminal đang mở:

```bash
# macOS, Linux, Git Bash
export WEBHOOK_URL='https://discord.com/api/webhooks/<id>/<token>'
```

```powershell
# Windows PowerShell
$env:WEBHOOK_URL = 'https://discord.com/api/webhooks/<id>/<token>'
```

Thay cả chuỗi bằng URL bạn đã copy. Biến này mất khi đóng terminal; mở terminal mới thì đặt lại.

## 2. Viết tin nhắn vào file JSON

Tạo file `message.json`:

```json
{
  "content": "Xin chào! Đây là tin nhắn đầu tiên gửi bằng webhook."
}
```

Đưa JSON vào file giúp tránh lỗi nháy kép/nháy đơn của từng loại terminal.

## 3. Gửi

```bash
# macOS, Linux, Git Bash
curl -i -H "Content-Type: application/json" -d @message.json "$WEBHOOK_URL"
```

```powershell
# Windows PowerShell
curl.exe -i -H "Content-Type: application/json" -d "@message.json" $env:WEBHOOK_URL
```

- `-H "Content-Type: application/json"`: báo cho Discord body là JSON.
- `-d @message.json`: lấy body từ file. Có `-d` thì curl tự dùng method `POST`.
- Trong PowerShell, `"@message.json"` phải có nháy kép vì `@` có nghĩa riêng.

Thành công thì dòng đầu là `HTTP/1.1 204 No Content` (hoặc `HTTP/2 204`) và tin nhắn hiện trong kênh.

## 4. Lấy lại tin vừa gửi: `?wait=true`

Thêm `?wait=true` vào cuối URL thì Discord trả `200 OK` kèm JSON của tin nhắn, trong đó có `id`:

```bash
curl -s -H "Content-Type: application/json" -d @message.json "$WEBHOOK_URL?wait=true"
```

```powershell
curl.exe -s -H "Content-Type: application/json" -d "@message.json" "$($env:WEBHOOK_URL)?wait=true"
```

`id` của tin nhắn dùng khi muốn sửa hoặc xóa đúng tin đó sau này.

## 5. Khi bị lỗi

| Bạn thấy | Nguyên nhân thường gặp |
| --- | --- |
| `400` và `"Cannot send an empty message"` | Thiếu `content` (và không có `embeds`) |
| `400` và `"message": "The request body contains invalid JSON."` | JSON sai cú pháp: kiểm tra lại như bài JSON |
| `400` và lỗi `content` quá dài | `content` tối đa 2000 ký tự |
| `401` hoặc `404` và `"Unknown Webhook"` | URL sai, thiếu ký tự, hoặc webhook đã bị xóa |
| `curl: (3) URL rejected` / không có gì xảy ra | Biến `WEBHOOK_URL` chưa đặt trong terminal này |

## Tóm tắt

- Đặt URL vào biến môi trường, viết tin nhắn vào file JSON.
- `curl -H "Content-Type: application/json" -d @message.json "$WEBHOOK_URL"` → `204` là xong.
- Thêm `?wait=true` để nhận lại tin nhắn vừa gửi.

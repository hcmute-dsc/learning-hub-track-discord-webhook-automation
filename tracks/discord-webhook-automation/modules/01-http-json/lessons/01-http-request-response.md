---
uid: 3385c197-aaa1-4351-a1b2-0c3bae1fb3f6
slug: http-request-response
title: "Request và response"
estimatedMinutes: 25
objectives:
  - "Kể được 4 phần của một HTTP request: method, URL, header, body"
  - "Đọc được status code và biết nhóm 2xx, 4xx, 5xx nghĩa là gì"
  - "Dùng curl -i để xem cả header lẫn body của response"
authors: [github:TienNHM]
---

# Request và response

Mỗi lần bạn mở một trang web hay gửi tin nhắn qua webhook, máy bạn gửi một **request** (yêu cầu) tới server, và server trả lại một **response** (phản hồi). Giao thức của cuộc trao đổi này là **HTTP**.

## Request gồm gì

| Phần | Ví dụ | Ý nghĩa |
| --- | --- | --- |
| Method | `GET`, `POST` | Muốn làm gì: `GET` để lấy dữ liệu, `POST` để gửi dữ liệu mới |
| URL | `https://discord.com/api/v10/gateway` | Gửi tới đâu |
| Header | `Content-Type: application/json` | Thông tin kèm theo, dạng `Tên: giá trị` |
| Body | `{"content": "Xin chào"}` | Dữ liệu gửi đi (thường chỉ có ở `POST`) |

## Response gồm gì

- **Status code**: một số 3 chữ số cho biết kết quả.
- **Header**: ví dụ `Content-Type: application/json` cho biết body là JSON.
- **Body**: dữ liệu trả về (có thể rỗng).

Các status code bạn sẽ gặp trong track này:

| Code | Nghĩa | Khi nào gặp với webhook |
| --- | --- | --- |
| `200 OK` | Thành công, có body | Gửi tin kèm `?wait=true` |
| `204 No Content` | Thành công, body rỗng | Gửi tin bình thường |
| `400 Bad Request` | Request sai | JSON sai cú pháp, tin nhắn rỗng, quá dài |
| `401` / `404` | Không có quyền / không tìm thấy | Webhook URL sai hoặc webhook đã bị xóa |
| `429 Too Many Requests` | Gửi quá nhanh | Vượt rate limit (chương 7) |
| `5xx` | Lỗi phía server | Discord gặp sự cố, thử lại sau |

Quy tắc nhớ nhanh: **2xx** là thành công, **4xx** là lỗi do bạn gửi, **5xx** là lỗi phía server.

## Thử với curl

`curl` là công cụ gửi HTTP request từ terminal. Thử gọi một địa chỉ công khai của Discord, không cần đăng nhập:

```bash
curl -i https://discord.com/api/v10/gateway
```

Trên Windows PowerShell, gõ `curl.exe` thay cho `curl`.

Tùy chọn `-i` in cả status và header. Kết quả có dạng (rút gọn):

```text
HTTP/1.1 200 OK
Content-Type: application/json
...

{"url":"wss://gateway.discord.gg"}
```

Dòng đầu là status code `200`, rồi tới các header, một dòng trống, và cuối cùng là body JSON.

Bỏ `-i` thì curl chỉ in body. Thêm `-v` để xem cả request mà curl gửi đi (dòng bắt đầu bằng `>`).

## Tóm tắt

- Request = method + URL + header + body. Response = status code + header + body.
- `GET` lấy dữ liệu, `POST` gửi dữ liệu. Gửi tin nhắn qua webhook là một `POST`.
- `curl -i <url>` để xem cả status, header và body.

---
uid: bedde3f9-cb62-4ed7-87cc-99e81cdd386d
slug: what-is-a-webhook
title: "Webhook là gì"
estimatedMinutes: 15
objectives:
  - "Giải thích webhook khác bot Discord ở điểm nào"
  - "Tạo một webhook trong server riêng và copy được webhook URL"
  - "Biết vì sao webhook URL phải giữ bí mật"
authors: [github:TienNHM]
---

# Webhook là gì

**Webhook** của Discord là một địa chỉ URL gắn với **một kênh**. Ai gửi `POST` có body JSON hợp lệ tới URL đó thì tin nhắn hiện trong kênh. Không cần đăng nhập, không cần chạy bot.

CLB đang dùng chính cách này: Learning Hub báo bài chờ review và báo mỗi lần deploy vào Discord bằng webhook.

## Webhook hay bot?

| | Webhook | Bot |
| --- | --- | --- |
| Chiều | Chỉ **gửi** tin vào một kênh | Gửi, đọc, trả lời, phản ứng với lệnh |
| Cần chạy liên tục | Không, gửi một request là xong | Có, bot phải luôn kết nối |
| Độ khó | Một lệnh `curl` | Cần thư viện, token, quyền |
| Hợp với | Thông báo, nhắc lịch, báo lỗi | Trò chuyện, lệnh `/`, quản lý server |

Track này chỉ dùng webhook: đủ cho mọi việc "báo cho mọi người biết".

## Tạo webhook

Làm trong **server riêng** của bạn (xem tab **Cài đặt**), không làm trong server CLB.

1. Mở server, bấm tên server ở góc trên trái → **Server Settings**.
2. Chọn **Integrations** → **Webhooks** → **New Webhook**.
3. Bấm vào webhook vừa tạo: đặt tên (ví dụ `Lab Bot`) và chọn kênh `#general`.
4. Bấm **Copy Webhook URL**, rồi **Save Changes**.

URL có dạng:

```text
https://discord.com/api/webhooks/<id>/<token>
```

Phần `<id>` là số định danh của webhook, phần `<token>` là **mật khẩu** của nó.

## Webhook URL là bí mật

Ai có URL đầy đủ đều làm được những việc sau, không cần tài khoản:

- gửi tin vào kênh của bạn, kể cả tin rác hoặc lừa đảo;
- đổi tên, đổi ảnh hoặc xóa chính webhook đó.

Vì vậy: không dán URL vào chat, không chụp màn hình có URL, không commit lên GitHub. Chương 5 nói kỹ cách giữ và cách thu hồi khi lỡ lộ.

## Tóm tắt

- Webhook = một URL gắn với một kênh; `POST` JSON vào đó là có tin nhắn.
- Tạo ở **Server Settings → Integrations → Webhooks**.
- Webhook URL chứa token, phải giữ như mật khẩu.

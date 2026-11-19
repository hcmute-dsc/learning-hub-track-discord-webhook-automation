---
uid: 5d8f8901-c3d7-4a8c-aee0-d7099c9217cc
slug: webhook-url-is-a-secret
title: "Webhook URL là secret"
estimatedMinutes: 20
objectives:
  - "Giữ secret ngoài Git bằng .env và .gitignore, và tự kiểm tra trước khi commit"
  - "Thu hồi đúng cách khi webhook URL bị lộ"
authors: [github:TienNHM]
---

# Webhook URL là secret

**Secret** là bất cứ thứ gì cho phép người khác hành động thay bạn: mật khẩu, token, API key, và **webhook URL**. Như đã nói ở chương 2, ai có webhook URL đều gửi tin được vào kênh của bạn, và đổi hoặc xóa được webhook.

## Giữ secret ngoài Git

1. Secret để trong biến môi trường hoặc file `.env`, **không** viết thẳng vào code.
2. Thêm `.env` vào `.gitignore` **trước** lần commit đầu tiên.
3. Commit kèm một file mẫu `.env.example` có tên biến nhưng không có giá trị thật:

   ```text
   WEBHOOK_URL=
   ```

4. Trước mỗi lần commit, xem lại những gì sắp được commit:

   ```bash
   git status
   git diff --staged
   ```

   `.env` không được có trong danh sách, và không có dòng nào chứa `discord.com/api/webhooks/`.

## Khi lỡ lộ

Lộ là khi URL đã nằm trên GitHub (kể cả trong commit cũ hay repo private), trong chat, trong ảnh chụp màn hình hay video.

1. **Thu hồi ngay:** Discord → **Server Settings → Integrations → Webhooks** → chọn webhook → **Delete Webhook**. Từ lúc này URL cũ trả `404 Unknown Webhook`, ai giữ nó cũng vô dụng.
2. Tạo webhook mới, đặt URL mới vào `.env` hoặc GitHub Secrets.
3. Rồi mới dọn chỗ bị lộ: xóa tin nhắn, sửa code.

Vì sao phải thu hồi trước? Xóa URL khỏi code bằng một commit mới **không** xóa nó khỏi lịch sử Git: ai cũng xem lại được commit cũ. Bot quét GitHub tìm secret chỉ cần vài phút sau khi bạn push. Thu hồi là cách duy nhất chắc chắn.

## Tóm tắt

- Secret ở `.env` hoặc biến môi trường; `.env` nằm trong `.gitignore`; commit `.env.example` thay thế.
- `git diff --staged` trước khi commit.
- Lộ thì **xóa webhook trong Discord trước**, dọn dẹp sau.

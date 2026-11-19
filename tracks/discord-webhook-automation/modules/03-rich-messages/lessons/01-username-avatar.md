---
uid: 627e5bb8-3b9a-4fc0-9ebd-a5d0f6392a2d
slug: username-avatar
title: "Tên và ảnh của người gửi"
estimatedMinutes: 10
objectives:
  - "Đổi tên và ảnh người gửi cho từng tin nhắn bằng username và avatar_url"
  - "Biết giới hạn của content và username"
authors: [github:TienNHM]
---

# Tên và ảnh của người gửi

Mặc định tin nhắn hiện tên và ảnh bạn đặt cho webhook. Mỗi tin nhắn có thể ghi đè hai thứ này:

```json
{
  "username": "Nhắc lịch CLB",
  "avatar_url": "https://github.com/hcmute-dsc.png",
  "content": "Tối nay 19:00 sinh hoạt CLB nhé!"
}
```

- `username`: tên hiện cho **tin nhắn này**, không đổi tên webhook. Tên không được chứa chữ `discord` hoặc `clyde`; nếu có, Discord trả `400`.
- `avatar_url`: link **công khai** tới một ảnh PNG/JPG. Link cần đăng nhập mới xem được thì ảnh không hiện.
- `content`: tối đa **2000** ký tự. Hỗ trợ Markdown của Discord: `**đậm**`, `*nghiêng*`, `` `code` ``, `[chữ](https://link)`.

Một webhook có thể dùng cho nhiều "nhân vật": cùng URL, mỗi script gửi một `username` khác nhau, ví dụ `Báo deploy`, `Nhắc lịch`.

## Thử

Sửa `message.json` như ví dụ trên và gửi lại bằng lệnh `curl` của chương 2.

---
uid: acaa8410-2892-4d76-b347-c7c77e9a0e01
slug: allowed-mentions
title: "Mention an toàn với allowed_mentions"
estimatedMinutes: 15
objectives:
  - "Giải thích vì sao tin nhắn tự động có thể vô tình ping cả server"
  - "Dùng allowed_mentions để tắt mọi mention, hoặc chỉ cho phép một người"
authors: [github:TienNHM]
---

# Mention an toàn với allowed_mentions

Nếu `content` có `@everyone`, `@here`, `<@id người dùng>` hay `<@&id role>`, Discord sẽ **ping** (gửi thông báo) cho những người đó.

Với tin nhắn tự động, nội dung thường lấy từ nơi khác: tiêu đề PR, tên bài viết RSS, tin nhắn người dùng gõ. Chỉ cần ai đó đặt tiêu đề PR là `@everyone xem gấp`, cả server bị ping.

## Tắt mọi mention

Thêm `allowed_mentions` với `parse` rỗng. Chữ `@everyone` vẫn hiện, nhưng không ai bị ping:

```json
{
  "content": "PR mới: @everyone xem gấp",
  "allowed_mentions": { "parse": [] }
}
```

**Quy tắc của track:** mọi tin nhắn tự động đều gửi kèm `"allowed_mentions": { "parse": [] }`, trừ khi bạn thật sự muốn ping.

## Chỉ ping đúng người

Muốn ping một người cụ thể (ví dụ mentor được giao review), liệt kê id của họ trong `users`:

```json
{
  "content": "<@123456789012345678> có bài mới chờ review",
  "allowed_mentions": { "users": ["123456789012345678"] }
}
```

Lấy id: bật **User Settings → Advanced → Developer Mode**, rồi chuột phải vào tên người đó → **Copy User ID**.

`parse` nhận các giá trị `"users"`, `"roles"`, `"everyone"`. Không dùng `parse: ["users"]` cùng với danh sách `users` trong một tin nhắn: Discord báo lỗi `400`.

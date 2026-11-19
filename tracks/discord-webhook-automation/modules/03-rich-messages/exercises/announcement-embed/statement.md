Soạn một tin nhắn thông báo **sự kiện của CLB** (có thể là sự kiện tưởng tượng) bằng embed, và gửi vào **server riêng** của bạn.

## Yêu cầu

- `username` riêng cho thông báo, ví dụ `Nhắc lịch CLB`.
- Một embed có: `title`, `description`, `color`, ít nhất 2 `fields` (ví dụ Thời gian, Địa điểm), `footer` và `timestamp`.
- Có `"allowed_mentions": { "parse": [] }`.

## Nộp bài

Tạo một [GitHub Gist](https://gist.github.com/) (public hoặc secret) gồm:

1. `announcement.json`: nội dung JSON bạn đã gửi.
2. Ảnh chụp tin nhắn hiển thị trong Discord. Kéo ảnh vào ô comment của gist để đính kèm.

Nộp link gist. **Không** đưa webhook URL vào gist hay ảnh chụp.

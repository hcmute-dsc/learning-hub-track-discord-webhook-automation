Làm theo bài **Gửi tin nhắn đầu tiên bằng curl**, trong server riêng của bạn.

1. Đặt biến `WEBHOOK_URL`.
2. Gửi một tin nhắn giới thiệu bản thân từ file `message.json`, kiểm tra status là `204`.
3. Gửi lại với `?wait=true`, tìm trường `id` trong kết quả.
4. Sửa `message.json` cho sai (ví dụ thêm dấu phẩy thừa), gửi lại và đọc lỗi `400`. Nhớ sửa lại file cho đúng.

Tick từng mục khi đã làm xong.

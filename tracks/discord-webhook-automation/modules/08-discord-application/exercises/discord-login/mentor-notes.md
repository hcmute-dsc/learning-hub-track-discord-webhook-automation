## Khi review bài này

- Tìm secret trong lịch sử commit: `git log -p | grep -i "client_secret\|discord.com/api/webhooks/"`. Thấy secret thật thì báo thành viên đặt lại secret **trước**, rồi mới góp ý phần còn lại.
- Đọc link chuyển hướng sang Discord: `scope` chỉ có `identify`, và có `state`.
- Ở `/callback`: so `state` trước khi đổi `code`. Hay gặp lỗi tạo `state` nhưng không so, hoặc so sau khi đã đổi `code`.
- Thử bấm **Hủy bỏ** nếu thành viên có bản chạy được, hoặc đọc code xử lý `error=access_denied`.
- Tin chào mừng: mention phải nằm trong `content` thì mới ping, và `allowed_mentions.users` chỉ có id của người vừa đăng nhập. Không dùng `parse: ["users"]`.
- Ảnh trong README phải che id. Id không phải secret, nhưng đây là thói quen tốt khi chia sẻ ảnh chụp.

## Góp ý nên tránh

- Bắt dùng framework cụ thể. Script thư viện chuẩn như trong bài là đủ.
- Đòi lưu đăng nhập bằng cookie hay session. Bài này chỉ cần hiện tên sau một lần đăng nhập.

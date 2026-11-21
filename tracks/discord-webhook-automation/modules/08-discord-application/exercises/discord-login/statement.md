Làm một app nhỏ cho người dùng **đăng nhập bằng Discord**, rồi chào họ bằng một tin nhắn trong server Discord riêng của bạn.

Có thể bắt đầu từ `login.py` trong bài **Đăng nhập bằng Discord với OAuth2**, hoặc viết bằng Node.js hay framework bạn quen. Viết lại từ đầu cũng được, miễn là bạn hiểu từng bước.

## Yêu cầu

- Tạo Discord application của riêng bạn, khai báo redirect, chỉ xin scope `identify`.
- Mở trang chủ thì được chuyển sang Discord. Đồng ý xong, trang hiện tên hiển thị và id Discord của người dùng.
- Kiểm tra `state` ở `/callback`. Người dùng bấm **Hủy bỏ** thì hiện thông báo dễ hiểu, không báo lỗi khó hiểu.
- Escape tên người dùng trước khi đưa vào HTML.
- `DISCORD_CLIENT_ID`, `DISCORD_CLIENT_SECRET` và `WEBHOOK_URL` đọc từ biến môi trường hoặc `.env`. `.env` phải có trong `.gitignore`.
- Đăng nhập thành công thì gửi vào kênh một tin chào mừng qua webhook, tag người vừa đăng nhập bằng `<@id>`, với `allowed_mentions` chỉ chứa id đó.

## Nộp bài

Link repo GitHub public. README gồm:

- các bước tạo application và redirect cần khai báo;
- các biến môi trường cần đặt và lệnh chạy;
- ảnh chụp (hoặc GIF) trang sau khi đăng nhập và tin chào mừng trong Discord. **Che id** trong ảnh.

Trước khi push, tìm secret trong toàn bộ lịch sử commit: `git log -p | grep -i "client_secret\|discord.com/api/webhooks/"`. Lỡ commit Client Secret thì bấm **Đặt lại Secret** trên Developer Portal trước, rồi mới sửa repo (giống cách xử lý webhook URL ở chương 5).

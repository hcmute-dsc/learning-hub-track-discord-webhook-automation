Viết một script **Python hoặc Node.js** nhận nội dung từ dòng lệnh và gửi một tin nhắn có **embed** vào server riêng của bạn.

## Yêu cầu

- Chạy được bằng một lệnh, ví dụ `python notify.py "Tiêu đề" "Nội dung"` hoặc `node --env-file=.env notify.mjs "Tiêu đề" "Nội dung"`.
- Tiêu đề vào `title` của embed, nội dung vào `description`. Thêm `color`, `footer` và `timestamp` là thời điểm gửi.
- Đọc `WEBHOOK_URL` từ biến môi trường (hoặc `.env`). Thiếu thì in thông báo và thoát với mã lỗi khác 0.
- Discord trả lỗi hoặc mất mạng: in status và nội dung lỗi, không in cả traceback dài.
- Có `allowed_mentions` chặn mention.

## Nộp bài

Một repo GitHub public gồm script, `.gitignore` (có `.env`) và `README.md`: script làm gì, cài gì, chạy thế nào, kèm ảnh chụp tin nhắn trong Discord.

Trước khi push, tìm trong toàn bộ lịch sử commit: `git log -p | grep -i "discord.com/api/webhooks/"` (Git Bash, macOS, Linux) hoặc `git log -p | Select-String "discord.com/api/webhooks/"` (PowerShell). Không được có kết quả nào là URL thật. Lỡ commit rồi thì làm theo chương 5: **xóa webhook trong Discord trước**, rồi mới sửa repo.

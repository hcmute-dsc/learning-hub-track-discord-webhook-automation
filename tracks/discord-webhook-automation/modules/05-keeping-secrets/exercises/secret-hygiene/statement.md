Áp dụng chương 5 vào repo bài **Script gửi thông báo** của bạn.

1. Kiểm tra `.gitignore` và thêm `.env.example`.
2. Tìm webhook URL trong toàn bộ lịch sử: `git log -p | grep -i "discord.com/api/webhooks/"` (PowerShell: `git log -p | Select-String "discord.com/api/webhooks/"`). Thấy URL thật thì **xóa webhook đó trong Discord**, tạo cái mới.
3. Tạo secret `DISCORD_WEBHOOK_URL` và workflow `notify.yml` như bài **GitHub Secrets cho GitHub Actions**, chạy thử từ tab **Actions**.
4. Tạo một webhook tạm, gửi một tin, rồi xóa nó và gửi lại bằng URL cũ để thấy lỗi `404`.

Tick từng mục khi đã làm xong.

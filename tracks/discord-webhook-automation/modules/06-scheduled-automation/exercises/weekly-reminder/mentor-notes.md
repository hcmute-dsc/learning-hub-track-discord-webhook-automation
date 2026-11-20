## Khi review bài này

- Đổi cron sang giờ Việt Nam và so với README. Lỗi hay gặp nhất là quên trừ 7 tiếng, hoặc trừ mà không lùi ngày.
- Mở link lần chạy trong README: phải là lần chạy xanh của chính workflow này.
- Tìm webhook URL trong lịch sử commit: `git log -p | grep -i "discord.com/api/webhooks/"`.
- Job báo lỗi: thiếu `if: failure()` thì job chỉ chạy khi thành công, sai ý đồ. Gợi ý thành viên thử bằng cách tạm cho job chính `exit 1`.

## Góp ý nên tránh

- Bắt đổi giờ chạy theo ý mentor. Giờ nào cũng được nếu cron khớp với README.

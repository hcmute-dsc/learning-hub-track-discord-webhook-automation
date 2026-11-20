## Khi review bài này

- Chạy thử bằng **webhook trong server riêng của mentor**: fork repo, thêm secret `DISCORD_WEBHOOK_URL`, bấm **Run workflow**. Không chạy được theo README thì trừ ở tiêu chí đầu.
- Tìm webhook URL thật trong toàn bộ lịch sử: `git log -p | grep -i "discord.com/api/webhooks/"`. Có URL thật thì tiêu chí secret là 0, và nhắc thành viên xóa webhook đó trước khi sửa.
- Thử lỗi: đặt secret thành URL sai một ký tự, chạy lại; workflow phải đỏ, job báo lỗi phải chạy (nó sẽ cũng lỗi vì URL sai, điều đó bình thường; xem log để thấy nó đã chạy).
- Bot "Báo PR mới" dùng `pull_request_target`: hỏi thành viên vì sao. Nếu workflow checkout code của PR và chạy nó với secret, đó là lỗ hổng; yêu cầu sửa.

## Mốc điểm gợi ý

- 90+: chạy ổn định nhiều tuần, README để người khác tự cài được.
- 70–89: đủ yêu cầu, còn vài chỗ chưa gọn.
- Dưới 70: thiếu chạy tự động, lộ secret, hoặc không có thử lại.

## Góp ý nên tránh

- Đòi thêm tính năng ngoài yêu cầu. Chấm theo rubric; ý tưởng mở rộng thì ghi là gợi ý.

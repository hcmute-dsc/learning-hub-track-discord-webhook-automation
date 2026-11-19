Làm một workflow GitHub Actions tự gửi **nhắc lịch hằng tuần** vào server Discord riêng của bạn. Có thể là lịch sinh hoạt CLB, lịch học nhóm, hay nhắc nộp bài.

## Yêu cầu

- `schedule` vào một giờ cố định theo **giờ Việt Nam** do bạn chọn (đổi đúng sang UTC), kèm `workflow_dispatch`.
- Tin nhắn có embed (`title`, `description`, `color`) và `allowed_mentions` chặn mention.
- Webhook URL lấy từ secret `DISCORD_WEBHOOK_URL`.
- Một job thứ hai, `if: failure()`, báo vào Discord khi job gửi nhắc lịch bị lỗi, có link tới lần chạy.

## Nộp bài

Link repo GitHub public. README gồm:

- giờ chạy theo giờ Việt Nam và dòng cron tương ứng;
- link tới một lần chạy thành công (tab **Actions**, chạy bằng **Run workflow** cũng được);
- ảnh chụp tin nhắn trong Discord.

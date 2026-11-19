---
uid: 6c2a8655-b92f-4900-a150-5b65457881ee
slug: cron-schedule
title: "Chạy workflow theo lịch với cron"
estimatedMinutes: 30
objectives:
  - "Đọc và viết biểu thức cron 5 trường"
  - "Đổi giờ Việt Nam sang giờ UTC cho schedule của GitHub Actions"
  - "Viết workflow tự gửi nhắc lịch vào Discord mỗi tuần"
authors: [github:TienNHM]
---

# Chạy workflow theo lịch với cron

Chương 5 có workflow chạy bằng tay (`workflow_dispatch`). Thêm `schedule` thì GitHub tự chạy nó theo giờ hẹn, kể cả khi máy bạn tắt.

## Biểu thức cron

Cron có 5 trường, cách nhau bằng dấu cách:

```text
┌───────────── phút (0–59)
│ ┌─────────── giờ (0–23)
│ │ ┌───────── ngày trong tháng (1–31)
│ │ │ ┌─────── tháng (1–12)
│ │ │ │ ┌───── thứ trong tuần (0–6, 0 = Chủ nhật)
│ │ │ │ │
0 12 * * 4
```

`*` nghĩa là "mọi giá trị". Vài ví dụ:

| Cron | Nghĩa (giờ UTC) |
| --- | --- |
| `0 12 * * 4` | 12:00 mỗi thứ Năm |
| `30 1 * * 1-5` | 01:30 từ thứ Hai tới thứ Sáu |
| `0 */6 * * *` | Mỗi 6 tiếng, vào phút 0 |
| `0 0 1 * *` | 00:00 ngày 1 mỗi tháng |

Thử cron của bạn trên [crontab.guru](https://crontab.guru/) trước khi commit.

## Giờ UTC, không phải giờ Việt Nam

GitHub Actions hiểu cron theo **UTC**. Việt Nam là **UTC+7**, nên lấy giờ Việt Nam **trừ 7**:

| Muốn chạy lúc (giờ VN) | Cron (UTC) |
| --- | --- |
| 19:00 thứ Năm | `0 12 * * 4` |
| 08:00 thứ Hai | `0 1 * * 1` |
| 06:00 thứ Hai | `0 23 * * 0` (23:00 **Chủ nhật** UTC) |

Ví dụ cuối: trừ 7 tiếng làm lùi sang ngày hôm trước, nên thứ cũng lùi một ngày.

## Workflow nhắc lịch

Tạo `.github/workflows/reminder.yml` trong repo đã có secret `DISCORD_WEBHOOK_URL` (chương 5):

```yaml
name: Weekly reminder

on:
  schedule:
    # 12:00 UTC thứ Năm = 19:00 giờ Việt Nam
    - cron: "0 12 * * 4"
  workflow_dispatch:

jobs:
  remind:
    runs-on: ubuntu-latest
    steps:
      - name: Send reminder to Discord
        env:
          WEBHOOK_URL: ${{ secrets.DISCORD_WEBHOOK_URL }}
        run: |
          jq -n '{
            username: "Nhắc lịch CLB",
            embeds: [{
              title: "Tối nay sinh hoạt CLB",
              description: "19:00 tại phòng A5-201. Nhớ mang laptop nhé!",
              color: 5793266
            }],
            allowed_mentions: {parse: []}
          }' > payload.json
          curl -fsS -H "Content-Type: application/json" -d @payload.json "$WEBHOOK_URL"
```

- Giữ `workflow_dispatch` để bấm **Run workflow** thử ngay, không phải chờ tới thứ Năm.
- `jq -n '{...}'` tạo JSON. Không có dữ liệu từ ngoài vào nên không cần `--arg`.

## Những điều cần biết về schedule

- Lịch chỉ chạy theo file workflow trên **nhánh mặc định** (thường là `main`). Sửa ở nhánh khác thì lịch không đổi.
- Giờ chạy có thể **trễ vài phút tới vài chục phút** khi GitHub đông việc, nhất là đúng đầu giờ. Đừng dùng cho việc cần đúng tới từng phút; chọn phút lẻ như `7 12 * * 4` để đỡ trễ.
- Khoảng cách ngắn nhất là **5 phút**.
- Repo **public** không có hoạt động nào trong **60 ngày** thì GitHub tự tắt các workflow có `schedule`. Vào tab **Actions** bật lại khi cần.
- Mỗi lần chạy trên repo public là miễn phí; repo private dùng vào số phút miễn phí hằng tháng của tài khoản.

## Tóm tắt

- Cron 5 trường: phút, giờ, ngày, tháng, thứ. Thử trên crontab.guru.
- Giờ trong cron là UTC: giờ Việt Nam trừ 7, coi chừng lùi sang ngày trước.
- Luôn kèm `workflow_dispatch` để chạy thử bằng tay.

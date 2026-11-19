---
uid: 229bb4c9-e358-4d77-b399-c248ec20a01c
slug: github-secrets
title: "GitHub Secrets cho GitHub Actions"
estimatedMinutes: 30
objectives:
  - "Lưu webhook URL vào GitHub Secrets của một repo"
  - "Dùng secret trong một workflow GitHub Actions chạy bằng tay"
  - "Biết secret được che trong log, và những giới hạn của nó"
authors: [github:TienNHM]
---

# GitHub Secrets cho GitHub Actions

Khi script chạy trên **GitHub Actions** thay vì trên máy bạn, không có file `.env`. Chỗ cất secret là **GitHub Secrets** của repo.

## 1. Tạo secret

Trong repo của bạn trên GitHub: **Settings → Secrets and variables → Actions → New repository secret**.

- Name: `DISCORD_WEBHOOK_URL`
- Secret: dán webhook URL (của server riêng)

Sau khi lưu, chính bạn cũng không xem lại được giá trị, chỉ thay mới được.

## 2. Dùng trong workflow

Tạo file `.github/workflows/notify.yml`:

```yaml
name: Notify Discord

on:
  workflow_dispatch:
    inputs:
      message:
        description: "Nội dung tin nhắn"
        required: true
        default: "Xin chào từ GitHub Actions!"

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send to Discord
        env:
          WEBHOOK_URL: ${{ secrets.DISCORD_WEBHOOK_URL }}
          MESSAGE: ${{ inputs.message }}
        run: |
          jq -n --arg text "$MESSAGE" '{content: $text, allowed_mentions: {parse: []}}' > payload.json
          curl -fsS -H "Content-Type: application/json" -d @payload.json "$WEBHOOK_URL"
```

- `workflow_dispatch`: chạy bằng tay ở tab **Actions** → **Notify Discord** → **Run workflow**, gõ nội dung.
- Secret được đưa vào **biến môi trường** `WEBHOOK_URL`, không viết thẳng `${{ secrets... }}` vào lệnh `run`.
- `jq -n --arg` tạo JSON an toàn từ nội dung người dùng gõ (có dấu `"` cũng không hỏng). `jq` có sẵn trên runner `ubuntu-latest`.
- `curl -f`: status lỗi (4xx, 5xx) làm bước này đỏ, nên bạn biết là gửi hỏng.

Commit file này, mở tab **Actions** và chạy thử. Tin nhắn hiện trong server riêng của bạn.

## 3. Secret trong log

GitHub tự thay giá trị secret bằng `***` trong log. Nhưng đừng dựa hẳn vào đó: in secret đã bị biến đổi (ví dụ cắt đôi, mã hoá base64) thì GitHub không nhận ra để che.

## 4. Giới hạn cần biết

- Workflow chạy từ **pull request của một fork** không đọc được secret của repo gốc. Đây là cố ý, để người lạ không lấy được secret bằng cách mở PR.
- Ai có quyền **sửa workflow** trong repo thì dùng được secret. Chỉ cấp quyền ghi cho người tin tưởng.

Chương 6 sẽ thêm `schedule` để workflow này tự chạy theo lịch, ví dụ nhắc lịch sinh hoạt mỗi tuần.

---
uid: c50ee7d6-64b1-405b-b370-88cf5d317e7a
slug: notify-on-failure
title: "Báo vào Discord khi workflow lỗi"
estimatedMinutes: 25
objectives:
  - "Dùng needs và if: failure() để chạy một job chỉ khi job trước lỗi"
  - "Đưa link tới lần chạy bị lỗi vào tin nhắn"
  - "Đưa dữ liệu từ GitHub vào JSON an toàn bằng jq --arg"
authors: [github:TienNHM]
---

# Báo vào Discord khi workflow lỗi

CLB dùng cách này cho Learning Hub: khi trang web không trả lời, kênh vận hành nhận tin "🔴" để người trực vào xem ngay. Bài này làm phiên bản đơn giản: báo khi workflow build/test bị lỗi.

## Job chỉ chạy khi có lỗi

```yaml
name: Build

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - name: Run tests
        run: ./run-tests.sh

  notify-failure:
    needs: test
    if: failure()
    runs-on: ubuntu-latest
    steps:
      - name: Tell Discord
        env:
          WEBHOOK_URL: ${{ secrets.DISCORD_WEBHOOK_URL }}
          RUN_URL: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
          REPO: ${{ github.repository }}
          COMMIT_MSG: ${{ github.event.head_commit.message }}
        run: |
          jq -n --arg repo "$REPO" --arg url "$RUN_URL" --arg msg "$COMMIT_MSG" '{
            embeds: [{
              title: ("🔴 Build lỗi: " + $repo),
              url: $url,
              description: ($msg | split("\n")[0]),
              color: 15548997
            }],
            allowed_mentions: {parse: []}
          }' > payload.json
          curl -fsS -H "Content-Type: application/json" -d @payload.json "$WEBHOOK_URL"
```

- `needs: test`: job `notify-failure` chờ job `test` xong mới chạy.
- `if: failure()`: chỉ chạy khi một job trong `needs` bị lỗi. Không có dòng này thì job chỉ chạy khi `test` **thành công**, ngược với điều bạn muốn.
- `RUN_URL`: link tới đúng lần chạy bị lỗi, bấm vào là thấy log.
- `COMMIT_MSG` có thể chứa dấu `"` hay xuống dòng, nên đưa vào bằng `jq --arg`, không ghép chuỗi. `split("\n")[0]` chỉ lấy dòng đầu của commit message.

Thay `./run-tests.sh` bằng lệnh test thật của repo bạn. Để thử, tạm sửa thành `exit 1`, push, rồi xem kênh Discord.

## Báo cả khi thành công?

Thường thì **không nên**: kênh đầy tin "✅ build xanh" thì không ai đọc, và tới lúc có tin lỗi thì cũng bị bỏ qua. Chỉ báo khi có việc cần người làm.

Các điều kiện khác dùng được trong `if:`

| Điều kiện | Chạy khi |
| --- | --- |
| `success()` (mặc định) | Mọi job trong `needs` thành công |
| `failure()` | Có job trong `needs` lỗi |
| `always()` | Luôn chạy, kể cả khi bị hủy |
| `cancelled()` | Workflow bị hủy |

## Không lộ secret qua tin nhắn

Tin nhắn chỉ nên có link tới lần chạy, tên repo, dòng đầu commit. **Đừng** dán log vào tin nhắn: log có thể chứa đường dẫn nội bộ hay dữ liệu thử nghiệm. Ai cần thì bấm link, GitHub sẽ kiểm tra quyền xem.

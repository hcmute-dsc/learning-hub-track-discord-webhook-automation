---
uid: 000ebdaf-d869-4b05-ac15-3c02dcc2bd0f
slug: project-guide
title: "Hướng dẫn dự án cuối"
estimatedMinutes: 20
objectives:
  - "Chọn một ý tưởng bot thông báo vừa sức"
  - "Biết bài nộp cần có gì và được chấm theo tiêu chí nào"
authors: [github:TienNHM]
---

# Hướng dẫn dự án cuối

Dự án cuối gom mọi thứ của track vào một sản phẩm chạy thật: một **bot thông báo** gửi vào Discord, tự chạy trên GitHub Actions.

## Chọn một ý tưởng

| Ý tưởng | Chạy khi | Gợi ý |
| --- | --- | --- |
| Nhắc lịch CLB | `schedule` hằng tuần | Đọc lịch từ file `events.json` trong repo, chỉ nhắc sự kiện của tuần đó |
| Báo PR mới | `pull_request` (types: `opened`) | Embed có tiêu đề PR, người mở, link. Lấy dữ liệu từ `github.event.pull_request` |
| Tóm tắt RSS | `schedule` hằng ngày | Đọc RSS của một blog, gửi các bài đăng trong 24 giờ qua, gộp tối đa 10 bài một tin |
| Ý tưởng của bạn | | Hỏi mentor trước khi làm |

Chọn cái bạn sẽ thật sự dùng. Bot nhắc lịch cho nhóm học của bạn có ích hơn một bot "demo".

**Lưu ý với "Báo PR mới":** workflow chạy từ PR của **fork** không đọc được secret (chương 5). Chỉ dùng cho PR mở từ nhánh trong chính repo của bạn. Đừng chuyển sang `pull_request_target` để "sửa" chuyện này khi chưa hiểu rủi ro: nó chạy với secret của repo trên code do người lạ gửi lên.

## Bài nộp cần có

1. **Repo GitHub public**, code Python hoặc Node.js, hoặc workflow dùng `jq` + `curl`.
2. Chạy **tự động** bằng GitHub Actions (`schedule` hoặc một sự kiện của repo), kèm `workflow_dispatch` để chạy thử.
3. Webhook URL trong **GitHub Secrets**; không có URL thật trong lịch sử commit.
4. Gửi có **thử lại có giới hạn** (chương 7) và thoát với mã lỗi khi thất bại.
5. Job **báo lỗi** với `if: failure()` (chương 6).
6. Tin nhắn có embed, `allowed_mentions` chặn mention, và đọc tốt trên điện thoại.
7. **README**: bot làm gì, chạy khi nào (giờ Việt Nam), cách cài vào repo khác, link một lần chạy thành công, ảnh chụp tin nhắn.

## Cách làm từng bước

1. Viết script chạy được trên máy với `.env` (chương 4).
2. Thêm thử lại (chương 7).
3. Đưa lên GitHub Actions với `workflow_dispatch`, secret trong GitHub Secrets (chương 5).
4. Thêm `schedule` hoặc sự kiện thật, và job báo lỗi (chương 6).
5. Viết README, chụp ảnh, nộp bài.

Bị kẹt ở bước nào thì quay lại chương đó, hoặc hỏi mentor trên Discord CLB.

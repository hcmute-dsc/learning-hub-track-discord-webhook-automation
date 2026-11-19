## Khi review bài này

- Chạy thử bằng **webhook trong server riêng của mentor**, theo đúng README. Không chạy được thì chưa đạt tiêu chí đầu.
- Tìm webhook URL thật trong cả lịch sử commit, không chỉ bản mới nhất: `git log -p | grep -i "discord.com/api/webhooks/"`. Có URL thật thì nhắc thành viên xóa webhook đó trong Discord và tạo cái mới, rồi mới sửa repo. Xóa khỏi code thôi là chưa đủ.
- Thử 3 trường hợp lỗi: không đặt `WEBHOOK_URL`, URL sai một ký tự, tắt mạng.

## Lỗi hay gặp

- Ghép JSON bằng f-string hoặc template string: hỏng khi nội dung có dấu `"`. Gợi ý `json.dumps` / `JSON.stringify`.
- Python dùng `urllib` không đặt `User-Agent` nên bị 403.
- Node dùng `fetch` nhưng không kiểm tra `response.ok`: script báo thành công dù Discord trả 400.

## Góp ý nên tránh

- Bắt thành viên đổi sang thư viện khác (`requests`, `axios`) khi script đã đúng yêu cầu.

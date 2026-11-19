## Khi review bài này

- Dán `announcement.json` vào `python -m json.tool` để chắc JSON hợp lệ.
- So ảnh chụp với JSON: màu viền, số field, footer phải khớp. Không có ảnh chụp thì chưa đạt tiêu chí đầu.
- Rà cả gist và ảnh chụp xem có lộ webhook URL không (chuỗi `discord.com/api/webhooks/`). Nếu có: nhắc thành viên **xóa webhook đó trong Discord và tạo cái mới** trước, rồi mới sửa gist.

## Lỗi hay gặp

- `color` ghi dạng chuỗi `"#5865F2"`: Discord trả 400. Gợi ý xem lại bài Embed.
- `timestamp` thiếu múi giờ (ví dụ `2026-11-19 19:00`): Discord không nhận. Cần dạng ISO 8601 như `2026-11-19T12:00:00Z`.

## Góp ý nên tránh

- Sửa JSON giúp thành viên. Chỉ ra trường bị lỗi để họ tự sửa.

---
uid: 80f35566-de4f-40f2-b1bd-75cfaa16d00e
slug: embeds
title: "Embed: thẻ thông tin có màu"
estimatedMinutes: 25
objectives:
  - "Tạo embed có title, description, url, color, fields, footer và timestamp"
  - "Đổi mã màu hex sang số thập phân cho trường color"
  - "Biết các giới hạn chính của embed"
authors: [github:TienNHM]
---

# Embed: thẻ thông tin có màu

**Embed** là khung thông tin có viền màu, tiêu đề, các cột thông tin. Bot báo deploy, báo PR mới thường dùng embed.

```json
{
  "username": "Nhắc lịch CLB",
  "embeds": [
    {
      "title": "Sinh hoạt CLB tuần 47",
      "url": "https://hcmute-dsc.io.vn",
      "description": "Chủ đề: **Tự động hoá với Discord webhook**",
      "color": 5793266,
      "fields": [
        { "name": "Thời gian", "value": "19:00, thứ Năm", "inline": true },
        { "name": "Địa điểm", "value": "Phòng A5-201", "inline": true },
        { "name": "Cần mang", "value": "Laptop đã cài curl" }
      ],
      "footer": { "text": "HCMUTE DSC Learning Hub" },
      "timestamp": "2026-11-19T12:00:00Z"
    }
  ]
}
```

| Trường | Ý nghĩa |
| --- | --- |
| `title`, `url` | Tiêu đề; có `url` thì tiêu đề thành link |
| `description` | Nội dung chính, dùng được Markdown |
| `color` | Màu viền, là **số thập phân** |
| `fields` | Các cột `name` / `value`; `inline: true` để xếp cạnh nhau |
| `footer.text` | Dòng nhỏ ở cuối |
| `timestamp` | Thời điểm theo chuẩn ISO 8601 (UTC có `Z`), Discord tự đổi sang giờ của người xem |

## Màu: từ hex sang số

Màu thường viết dạng hex như `#5865F2`, nhưng `color` cần số thập phân. Đổi bằng:

```bash
python -c "print(int('5865F2', 16))"
node -e "console.log(parseInt('5865F2', 16))"
```

Cả hai in `5793266`. Một vài màu hay dùng: xanh lá `#57F287` = `5763719`, đỏ `#ED4245` = `15548997`, vàng `#FEE75C` = `16705372`.

## Giới hạn chính

- Tối đa **10** embed mỗi tin nhắn, **25** field mỗi embed.
- `title` 256 ký tự, `description` 4096, field `name` 256, field `value` 1024.
- Tổng chữ trong mọi embed của một tin nhắn không quá **6000** ký tự.

Vượt giới hạn thì Discord trả `400` và chỉ ra trường bị lỗi.

Một tin nhắn có thể có cả `content` lẫn `embeds`: `content` hiện phía trên embed.

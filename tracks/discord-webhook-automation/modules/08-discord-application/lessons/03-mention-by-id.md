---
uid: d46daad7-6574-40e5-851c-a255192b24a9
slug: mention-by-id
title: "Tag đúng người bằng id Discord"
estimatedMinutes: 15
objectives:
  - "Dùng id lấy được từ OAuth2 để tag đúng người, và chỉ người đó, qua webhook"
  - "Biết mention trong embed không gửi thông báo, mention trong content thì có"
  - "Sửa một tin đã gửi qua webhook mà không làm ai bị ping lại"
authors: [github:TienNHM]
---

# Tag đúng người bằng id Discord

Ở bài trước, app lấy được `id` Discord của người dùng. Có id này thì webhook tag được **đúng người đó**, ngay cả khi tên Discord của họ khác tên trên trang web của bạn.

Ví dụ thật: sáng thứ Hai, Learning Hub đăng top 5 bảng xếp hạng tuần lên kênh CLB. Thành viên nào đã **Kết nối Discord** thì được tag, ai chưa kết nối thì hiện tên trên Learning Hub.

## Mention ở đâu thì được ping

Nhắc lại chương 3: `<@id>` trong tin nhắn là một mention. Discord xử lý mention khác nhau tùy chỗ đặt:

- Trong **`content`**: hiện thành tên người đó **và** gửi thông báo (ping), nếu `allowed_mentions` cho phép.
- Trong **embed** (`description`, `fields`, ...): vẫn hiện thành tên, nhưng **không** gửi thông báo.

Vì vậy, muốn chúc mừng có ping thì đặt mention trong `content`, còn bảng chi tiết để trong embed.

## Tạo tin nhắn từ danh sách

```python
def leaderboard_payload(members):
    """members: danh sách dict có name, done và discord_id (None nếu chưa kết nối)."""
    ids = [m["discord_id"] for m in members if m["discord_id"]]
    lines = []
    for rank, m in enumerate(members, start=1):
        who = f"<@{m['discord_id']}>" if m["discord_id"] else m["name"]
        lines.append(f"{rank}. {who}: {m['done']} bài")

    return {
        "content": "Chúc mừng top tuần này! " + " ".join(f"<@{i}>" for i in ids),
        "embeds": [{
            "title": "Bảng xếp hạng tuần",
            "description": "\n".join(lines),
            "color": 0xF97316,
        }],
        # Chỉ ping đúng những id này. Không có "parse", nên @everyone hay
        # một mention lạ nằm trong tên ai đó cũng không ping được ai.
        "allowed_mentions": {"users": ids},
    }
```

Với ba người, trong đó người thứ hai chưa kết nối Discord:

```python
members = [
    {"name": "An", "done": 7, "discord_id": "111111111111111111"},
    {"name": "Bình", "done": 5, "discord_id": None},
    {"name": "Chi", "done": 4, "discord_id": "333333333333333333"},
]
```

hàm trả về:

```json
{
  "content": "Chúc mừng top tuần này! <@111111111111111111> <@333333333333333333>",
  "embeds": [
    {
      "title": "Bảng xếp hạng tuần",
      "description": "1. <@111111111111111111>: 7 bài\n2. Bình: 5 bài\n3. <@333333333333333333>: 4 bài",
      "color": 16347926
    }
  ],
  "allowed_mentions": { "users": ["111111111111111111", "333333333333333333"] }
}
```

Gửi JSON này bằng hàm `send` của chương 7 là xong. Danh sách `users` tối đa 100 id.

## Tôn trọng người được tag

- **Chỉ tag người đã đồng ý.** Kết nối Discord chỉ cho biết "người này là ai", không có nghĩa là người đó muốn bị nêu tên trước cả server. Learning Hub tách riêng hai việc: kết nối Discord, và công tắc "Hiện tôi trên bảng xếp hạng".
- **Cho phép hủy kết nối.** Người dùng hủy thì xóa id của họ khỏi database của bạn.
- **Chỉ lưu thứ cần dùng:** id và tên. Không cần lưu access token nếu không gọi Discord thay người dùng nữa.

## Sửa tin đã gửi

Đăng bảng xong mới phát hiện thiếu một người? Không cần đăng tin mới, hãy **sửa** tin cũ.

1. Khi gửi, thêm `?wait=true` vào cuối webhook URL. Discord trả `200` kèm toàn bộ tin nhắn, trong đó có `id`, thay vì `204` không có nội dung. Lưu `id` này lại.
2. Khi cần sửa, gửi `PATCH` tới `<webhook URL>/messages/<id tin nhắn>` với nội dung mới:

```bash
curl -X PATCH "$WEBHOOK_URL/messages/$MESSAGE_ID" \
  -H "Content-Type: application/json" \
  -d @message.json
```

Tin được sửa tại chỗ và có thêm chữ "(đã chỉnh sửa)". Discord **không** gửi thông báo mới khi sửa tin, nên người đã được tag không bị ping lần nữa. Webhook chỉ sửa được tin do chính nó gửi.

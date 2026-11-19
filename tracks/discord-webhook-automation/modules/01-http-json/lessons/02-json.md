---
uid: 8493e195-dc9b-4f09-803c-72a266493e53
slug: json
title: "JSON trong 15 phút"
estimatedMinutes: 20
objectives:
  - "Viết đúng object, array và 4 kiểu giá trị của JSON"
  - "Tìm ra lỗi cú pháp JSON hay gặp"
  - "Kiểm tra một file JSON bằng python -m json.tool hoặc node"
authors: [github:TienNHM]
---

# JSON trong 15 phút

**JSON** là định dạng văn bản để trao đổi dữ liệu. Discord nhận tin nhắn dưới dạng JSON, và hầu hết API trên web cũng vậy.

## Hai cấu trúc

- **Object**: cặp `"khóa": giá trị`, đặt trong `{ }`, cách nhau bằng dấu phẩy.
- **Array**: danh sách giá trị, đặt trong `[ ]`.

```json
{
  "content": "Họp CLB lúc 19:00",
  "tts": false,
  "embeds": [
    { "title": "Lịch tuần này", "color": 5793266 }
  ],
  "thread_name": null
}
```

## Các kiểu giá trị

| Kiểu | Ví dụ |
| --- | --- |
| Chuỗi (string) | `"Xin chào"`, luôn dùng **nháy kép** |
| Số (number) | `42`, `3.14`, `-1` |
| Đúng/sai (boolean) | `true`, `false` (chữ thường) |
| Rỗng | `null` |
| Object, array | lồng nhau tùy ý |

## Lỗi hay gặp

```text
{ 'content': 'Xin chào' }      ← sai: nháy đơn
{ "content": "Xin chào", }     ← sai: dấu phẩy thừa ở cuối
{ content: "Xin chào" }        ← sai: khóa phải có nháy kép
{ "tts": False }               ← sai: phải là false
```

JSON không có comment. Muốn ghi chú thì ghi ở chỗ khác.

## Kiểm tra một file JSON

Lưu ví dụ ở trên vào `message.json`, rồi chạy một trong hai lệnh:

```bash
python -m json.tool message.json
node -e "console.log(JSON.parse(require('fs').readFileSync('message.json', 'utf8')))"
```

File đúng thì lệnh in lại nội dung. File sai thì báo lỗi kèm số dòng, ví dụ `Expecting property name enclosed in double quotes: line 1 column 3`.

## Tóm tắt

- Object `{ }` chứa cặp khóa–giá trị, array `[ ]` chứa danh sách.
- Khóa và chuỗi dùng nháy kép. Không có dấu phẩy thừa, không có comment.
- Luôn kiểm tra JSON trước khi gửi: phần lớn lỗi `400` từ Discord là do JSON sai.

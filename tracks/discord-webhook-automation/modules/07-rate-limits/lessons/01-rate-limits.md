---
uid: 2057040c-6d52-45c2-85c0-3dafabc67e4f
slug: rate-limits
title: "Rate limit: khi Discord bảo bạn chậm lại"
estimatedMinutes: 20
objectives:
  - "Nhận ra response 429 và đọc retry_after"
  - "Biết các header X-RateLimit-* cho biết còn gửi được bao nhiêu"
  - "Biết vì sao gửi lại liên tục khi bị lỗi làm tình hình tệ hơn"
authors: [github:TienNHM]
---

# Rate limit: khi Discord bảo bạn chậm lại

Mỗi webhook chỉ được gửi một số tin trong một khoảng thời gian ngắn. Gửi nhanh hơn thì Discord trả **`429 Too Many Requests`** và không đăng tin đó.

Gặp 429 khi: script gửi 50 tin trong một vòng lặp, nhiều workflow cùng gửi vào một webhook một lúc, hay một bug gửi lặp vô hạn.

## Response 429 trông thế nào

```text
HTTP/1.1 429 Too Many Requests
Content-Type: application/json
Retry-After: 1
X-RateLimit-Limit: 5
X-RateLimit-Remaining: 0
X-RateLimit-Reset-After: 0.512
X-RateLimit-Scope: user

{"message": "You are being rate limited.", "retry_after": 0.512, "global": false}
```

- `retry_after` (trong body JSON): số **giây** phải chờ trước khi gửi lại, có thể là số lẻ.
- `global: true` nghĩa là bị giới hạn chung cho mọi request, không chỉ webhook này.

## Header có trong mọi response

Ngay cả khi gửi thành công, Discord cũng kèm các header cho biết bạn còn bao nhiêu "lượt":

| Header | Ý nghĩa |
| --- | --- |
| `X-RateLimit-Limit` | Số request tối đa trong một đợt |
| `X-RateLimit-Remaining` | Còn lại bao nhiêu request trong đợt này |
| `X-RateLimit-Reset-After` | Bao nhiêu giây nữa thì đợt mới bắt đầu |

Xem bằng `curl -i` như chương 2. Con số cụ thể Discord có thể đổi bất cứ lúc nào, nên **đọc header hoặc `retry_after`**, đừng chép cứng con số vào code.

## Gửi lại liên tục là tự làm khó mình

Script gặp lỗi rồi gửi lại ngay, lỗi tiếp, lại gửi ngay... Discord đếm cả những request lỗi (401, 403, 429). Quá nhiều request lỗi trong vài phút thì **địa chỉ IP của bạn bị chặn tạm thời**, mọi request đều hỏng, kể cả request đúng. Trên GitHub Actions, IP dùng chung với người khác nên càng phải cẩn thận.

Quy tắc:

- **429**: chờ đúng `retry_after` rồi mới gửi lại.
- **5xx** hoặc mất mạng: chờ một chút, mỗi lần chờ lâu hơn, rồi gửi lại.
- **400, 401, 403, 404**: **không** gửi lại. Gửi y hệt thì lỗi y hệt; phải sửa JSON hoặc URL.
- Luôn có **số lần thử tối đa**, ví dụ 3, rồi bỏ cuộc và báo lỗi.

Bài sau viết đúng như vậy bằng code.

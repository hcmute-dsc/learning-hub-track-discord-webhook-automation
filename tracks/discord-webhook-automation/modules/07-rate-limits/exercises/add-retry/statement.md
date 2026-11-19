Nâng cấp script của bài **Script gửi thông báo** (chương 4) theo bài **Thử lại có giới hạn**.

Cách thử từng trường hợp mà không cần làm phiền Discord:

- **4xx**: đổi một ký tự trong token của `WEBHOOK_URL` → phải dừng ngay sau lần 1.
- **Mất mạng**: tắt Wi-Fi rồi chạy → phải thử 3 lần, chờ tăng dần, rồi thoát với mã lỗi.
- **429**: gửi 10 tin liền trong một vòng lặp vào server riêng của bạn; thường sẽ gặp 429 sau vài tin. Không làm việc này với webhook của CLB.

Tick từng mục khi đã làm xong. Commit thay đổi vào repo của bài notify-script.

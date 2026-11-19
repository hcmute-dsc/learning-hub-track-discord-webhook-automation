Làm các bước dưới đây **một lần**, trước bài học đầu tiên. Mất khoảng 20 phút.

## 1. Tài khoản và server Discord riêng

Mọi bài trong track gửi tin vào **server Discord của riêng bạn**, không gửi vào kênh của CLB.

1. Đăng nhập Discord (app hoặc <https://discord.com/app>).
2. Ở cột bên trái, bấm dấu **+** (Add a Server) → **Create My Own** → **For me and my friends**.
3. Đặt tên, ví dụ `webhook-lab`. Server mới có sẵn kênh `#general`, dùng kênh đó để thử.

Bạn là chủ server nên có quyền **Manage Webhooks**, cần cho chương 2.

## 2. curl

- **Windows 10/11**: có sẵn. Trong PowerShell gõ `curl.exe` (có `.exe`), vì `curl` trong PowerShell là tên khác của lệnh `Invoke-WebRequest`.
- **macOS**: có sẵn.
- **Ubuntu/Debian**: `sudo apt install curl`.

## 3. Python hoặc Node.js (chọn một, cho chương 4)

- **Python 3.10 trở lên**: <https://www.python.org/downloads/>. Trên Windows, tick **Add python.exe to PATH** khi cài.
- **Node.js 22 (LTS) trở lên**: <https://nodejs.org/>.

## 4. Git và tài khoản GitHub

Cần cho bài nộp ở chương 4 và GitHub Secrets ở chương 5. Chưa có thì xem track hoặc hướng dẫn Git của CLB.

## 5. Tự kiểm tra

```bash
curl --version
python --version
node --version
git --version
```

Trên Windows PowerShell, thay dòng đầu bằng `curl.exe --version`. Kết quả đúng: `curl` in ra `curl 7.x` hoặc `curl 8.x`, và ít nhất một trong `Python 3.10+` hoặc `v22.x` trở lên. Máy chỉ cài một trong hai (Python hoặc Node.js) thì lệnh kia báo không tìm thấy, vẫn được.

## 6. Khi bị lỗi

| Lỗi | Cách sửa |
| --- | --- |
| PowerShell báo lỗi lạ khi chạy `curl -H ...` | Dùng `curl.exe` thay cho `curl` |
| `python: command not found` / mở Microsoft Store | Cài lại Python và tick **Add python.exe to PATH**, rồi mở terminal mới |
| Không thấy mục **Integrations** trong Server Settings | Bạn không có quyền quản lý server đó. Tạo server riêng theo bước 1 |

Vẫn chưa được? Hỏi trong kênh Discord của CLB, kèm ảnh chụp lỗi (che webhook URL nếu có).

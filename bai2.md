
# CHUẨN HÓA TÀI LIỆU CÀI ĐẶT HỆ THỐNG

## Phần 1. Báo cáo phân tích (I/O)

* **Input (Văn bản thô):** 
  Các ghi chú kỹ thuật rời rạc về cách cài đặt cảm biến độ ẩm, mã nguồn điều khiển thiết bị bằng Python dạng thuần túy, và các thông số so sánh thiết bị không được căn chỉnh bố cục.
* **Output (Giao diện render chuyên nghiệp):** 
  Tài liệu kỹ thuật trực quan sử dụng **Inline Code** để chỉ rõ tên biến/lệnh ngắn, **Code Block với Syntax Highlighting** để phân biệt rõ câu lệnh Terminal và mã nguồn Python, cùng với **Table** và **Task List** giúp quản lý công việc và so sánh thiết bị trực quan, mạch lạc.

---

## Phần 2. Source code hoàn chỉnh (`README.md`)

```markdown
# Hướng dẫn Cài đặt Cảm biến Độ ẩm cho Trang trại Lan

## 1. Tổng quan hệ thống
Cảm biến độ ẩm giúp theo dõi độ ẩm không khí trong nhà lưới, đảm bảo môi trường tối ưu cho Lan phát triển.

---

## 2. Các bước cài đặt thiết bị

### Bước 1: Cập nhật hệ thống
Mở Terminal và chạy lệnh cập nhật danh sách gói:
\`\`\`bash
sudo apt update && sudo apt upgrade -y
\`\`\`

### Bước 2: Cài đặt thư viện cho cảm biến DHT22
Cài đặt thư viện đọc dữ liệu từ cảm biến bằng lệnh:
\`\`\`bash
sudo apt install -y python3-pip
sudo pip3 install Adafruit_DHT
\`\`\`

### Bước 3: Kích hoạt giao tiếp 1-Wire (nếu dùng DHT22)
Bật giao tiếp 1-Wire trên hệ thống thông qua công cụ cấu hình:
\`\`\`bash
sudo raspi-config
\`\`\`
*(Chọn: **Interface Options** -> **1-Wire**)*

---

## 3. Mã nguồn điều khiển máy bơm tự động (Python)
Dưới đây là đoạn mã Python sử dụng biến `PIN_DHT` và `THRESHOLD` để điều khiển hệ thống tưới nước tự động:

\`\`\`python
import Adafruit_DHT
import time

# Cấu hình chân GPIO và loại cảm biến
SENSOR = Adafruit_DHT.DHT22
PIN_DHT = 4  # Tương ứng GPIO4 trên Raspberry Pi
THRESHOLD = 75.0  # Ngưỡng độ ẩm kích hoạt máy bơm (%)

def check_humidity():
    humidity, temperature = Adafruit_DHT.read_retry(SENSOR, PIN_DHT)
    if humidity is not None:
        print(f"Nhiệt độ: {temperature:.1f}C - Độ ẩm: {humidity:.1f}%")
        if humidity < THRESHOLD:
            print("Độ ẩm thấp! Đang bật máy bơm tưới nước...")
        else:
            print("Độ ẩm ổn định. Tắt máy bơm.")
    else:
        print("Lỗi: Không đọc được dữ liệu từ cảm biến!")

if __name__ == "__main__":
    while True:
        check_humidity()
        time.sleep(10)
\`\`\`

---

## 4. Bảng so sánh Cảm biến Độ ẩm (Loại cũ vs Loại mới)

| Tiêu chí so sánh | Cảm biến cũ (Analog) | Cảm biến mới (DHT22 / Digital) |
| :--- | :--- | :--- |
| **Độ chính xác** | Thấp, dễ bị nhiễu tín hiệu. | **Cao**, sai số thấp, ổn định. |
| **Khoảng đo độ ẩm** | Hẹp (20% - 80%) | **Rộng (0% - 100%)** |
| **Giao tiếp dữ liệu** | Tín hiệu điện áp tương tự. | Giao tiếp kỹ thuật số chuẩn số hóa. |
| **Độ bền thiết bị** | Kém, dễ bị oxy hóa ngoài môi trường. | **Tốt hơn**, vỏ bọc chống ẩm hiệu quả. |

---

## 5. Task List: Kiểm tra phần cứng trước khi vận hành
- [x] Kiểm tra nguồn điện cấp cho Raspberry Pi ($5\text{V}$).
- [x] Đấu nối chính xác chân `VCC`, `GND`, `DATA` của cảm biến DHT22.
- [ ] Chạy thử nghiệm script Python đọc dữ liệu lần đầu.
- [ ] Cấu hình chạy tự động (Crontab) khi khởi động hệ thống.

# Hướng Dẫn Setup MTProtoProxy

MTProtoProxy là một proxy server hỗ trợ giao thức MTProto của Telegram, giúp người dùng truy cập Telegram thông suốt hơn ở những khu vực bị hạn chế.

---

## Bước 1: Cập nhật hệ thống

```bash
sudo apt update
```

## Bước 2: Cài đặt các gói cần thiết

```bash
sudo apt install git python3 python3-pip -y
```

## Bước 3: Clone mã nguồn MTProtoProxy

```bash
git clone https://github.com/alexbers/mtprotoproxy.git
cd mtprotoproxy
```

## Bước 4: Cập nhật tệp `requirements.txt`

Tạo hoặc chỉnh sửa file `requirements.txt` để thêm các thư viện cần thiết:

```bash
vi requirements.txt
```

Nội dung bên trong `requirements.txt`:

```
aiohttp
pysocks
configparser
```

## Bước 5: Cài đặt thư viện Python phụ thuộc

```bash
pip3 install -r requirements.txt
```

## Bước 6: Kiểm tra chạy thử proxy

```bash
python3 mtprotoproxy.py
```

> **Lưu ý:** Bạn có thể cần cấu hình thêm file `config.ini` để thiết lập token và cổng proxy nếu dùng trong môi trường thực tế.

---

## Bước 7: Tạo systemd service để chạy MTProtoProxy như một dịch vụ nền

Tạo file service:

```bash
sudo vi /etc/systemd/system/mtproxy.service
```

Nội dung bên trong:

```ini
[Unit]
Description=MTProto Proxy for Telegram
After=network.target

[Service]
WorkingDirectory=/home/ubuntu/mtprotoproxy
ExecStart=/usr/bin/python3 mtprotoproxy.py
Restart=always

[Install]
WantedBy=multi-user.target
```

> 📌 **Chú ý:** Đảm bảo đường dẫn `WorkingDirectory` và `ExecStart` đúng với vị trí cài đặt thực tế trên máy bạn.

## Bước 8: Kích hoạt và khởi động dịch vụ

```bash
sudo systemctl daemon-reload
sudo systemctl enable mtproxy
sudo systemctl start mtproxy
```

## Bước 9: Kiểm tra trạng thái dịch vụ

```bash
sudo systemctl status mtproxy
```

## Bước 10: Xem log hoạt động của proxy

```bash
journalctl -u mtproxy -f
```

---



---

## Bước 11: Ví dụ cấu hình file `config.ini`

Trước khi chạy proxy thực tế, bạn nên tạo file `config.ini` để cấu hình chi tiết:

```ini
PORT = 443  # Cổng lắng nghe

USERS = {
    # 'tên_người_dùng': 'secret_hex_32_ký_tự'
    'user1': '0123456789abcdef0123456789abcdef',
    'user2': 'abcdefabcdefabcdefabcdefabcdefab',
}

# Fake TLS domain (tuỳ chọn, dùng để tránh DPI)
TLS_DOMAIN = 'www.google.com'

# Log ra console
LOG_TO_CONSOLE = True

# Giới hạn số client từ 1 IP
CLIENTS_PER_USER = 100

# Timeout nếu client không gửi gì
IDLE_TIMEOUT = 300

# Chặn IP (tuỳ chọn)
# BLOCKED_IPS = ['192.168.1.100', '10.0.0.0/24']

```

### Tạo secret ngẫu nhiên

Bạn có thể tạo một `secret` bằng lệnh sau:

```bash
openssl rand -hex 16
```

---

## Tài Nguyên Tham Khảo

- Repository chính thức: [https://github.com/alexbers/mtprotoproxy](https://github.com/alexbers/mtprotoproxy)


# Bài 4: Quản lý tiến trình nền với nohup và tín hiệu Kill

## Mục tiêu
- Tạo kịch bản bash script chạy ngầm độc lập với phiên đăng nhập bằng `nohup`.
- Quản lý tiến trình, theo dõi file log và sử dụng tín hiệu `kill` để dừng tiến trình theo PID.

---

## 1. Khởi tạo Kịch bản Monitor `loop-monitor.sh`

Tạo file `loop-monitor.sh`:
```bash
cat << 'EOF' > loop-monitor.sh
#!/bin/bash

while true; do
    echo "System time: $(date)" >> /tmp/monitor.log
    sleep 5
done
EOF

chmod +x loop-monitor.sh
```

---

## 2. Khởi chạy Tiến trình Nền Độc lập bằng `nohup`

Chạy script dưới nền độc lập với phiên làm việc SSH:
```bash
nohup ./loop-monitor.sh > /dev/null 2>&1 &
```

---

## 3. Kiểm tra và Theo dõi Tiến trình

### Tra cứu PID của tiến trình:
```bash
$ pgrep -f loop-monitor.sh
15842
```

### Theo dõi kết quả ghi log thời gian thực (`tail -f /tmp/monitor.log`):
```bash
$ tail -n 5 /tmp/monitor.log
System time: Wed Oct  7 11:22:00 UTC 2026
System time: Wed Oct  7 11:22:05 UTC 2026
System time: Wed Oct  7 11:22:10 UTC 2026
System time: Wed Oct  7 11:22:15 UTC 2026
System time: Wed Oct  7 11:22:20 UTC 2026
```

---

## 4. Dừng Tiến trình (`kill`)

Gửi tín hiệu dừng tiến trình qua PID `15842`:
```bash
kill 15842
```

Xác nhận tiến trình đã bị dừng:
```bash
$ pgrep -f loop-monitor.sh
# Trả về rỗng (tiến trình đã kết thúc)
```

---

## 5. Kết luận
- `nohup` giúp tiến trình tiếp tục chạy ngay cả khi người dùng ngắt kết nối SSH (SIGHUP signal được bỏ qua).
- Dễ dàng quản lý và hủy tiến trình ngầm thông qua `pgrep` và `kill`.

# Bài 3: Thiết lập Cơ sở dữ liệu và Tự cấu hình dịch vụ Systemd cho Spring Boot

## 1. Thông tin cấu hình

- Database: `springboot_db`
- MySQL User: `spring-admin`
- MySQL Password: `SpringSecure@123`
- Linux User: `spring-runner`
- Application: `/opt/spring-app/app.jar`
- Application Port: `8082`
- Systemd Service: `spring-app.service`

## 2. Khởi tạo MySQL

Đã tạo database:

```text
springboot_db
```

Đã tạo user:

```text
spring-admin
```

và cấp toàn quyền trên database `springboot_db`.

## 3. Linux User

Ứng dụng được chạy bằng user non-root:

```text
spring-runner
```

User sử dụng:

```text
/usr/sbin/nologin
```

để không cho phép đăng nhập shell trực tiếp.

## 4. Systemd Service

Service được tạo tại:

```text
/etc/systemd/system/spring-app.service
```

Các cấu hình chính:

```text
User=spring-runner
ExecStart=/usr/bin/java -jar /opt/spring-app/app.jar
Restart=on-failure
RestartSec=10
SERVER_PORT=8082
```

## 5. Kiểm tra trạng thái service

Lệnh:

```bash
sudo systemctl status spring-app.service
```

Kết quả:

```text
Active: active (running)
```

## 6. Kiểm tra port

Lệnh:

```bash
sudo ss -tlnp | grep 8082
```

Kết quả cho thấy ứng dụng Java đang lắng nghe trên port:

```text
8082
```

## 7. Kiểm tra user chạy ứng dụng

Lệnh:

```bash
ps -eo user,pid,cmd | grep '[j]ava'
```

Kết quả cho thấy tiến trình Spring Boot chạy dưới user:

```text
spring-runner
```

## 8. Kiểm tra cấu hình Restart

Lệnh:

```bash
systemctl show spring-app.service -p User -p ExecStart -p Restart -p RestartUSec
```

Kết quả xác nhận:

```text
User=spring-runner
Restart=on-failure
RestartUSec=10s
```

## 9. Kết luận

Đã hoàn thành cấu hình Spring Boot chạy dưới user non-root `spring-runner`, tự động khởi động bằng Systemd và lắng nghe tại port `8082`.

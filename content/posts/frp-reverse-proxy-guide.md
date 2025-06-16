---
title: "Hướng dẫn cài đặt và sử dụng FRP (Fast Reverse Proxy)"
date: 2024-12-19T14:00:00+07:00
draft: false
description: "Hướng dẫn chi tiết cách cài đặt và cấu hình FRP để expose dịch vụ nội bộ ra Internet thông qua VPS"
tags: ["FRP", "Reverse Proxy", "VPS", "Network", "DevOps"]
categories: ["Hướng dẫn", "Network"]
author: "G.D"
---

# Hướng dẫn cài đặt và sử dụng FRP (Fast Reverse Proxy)

FRP là một công cụ mạnh mẽ giúp expose các dịch vụ nội bộ ra Internet thông qua VPS. Bài viết này sẽ hướng dẫn cách cài đặt và cấu hình FRP từ cơ bản đến nâng cao.

---

## Mục lục
1. [FRP là gì?](#frp-là-gì)
2. [Cài đặt FRP Server trên VPS](#cài-đặt-frp-server-trên-vps)
3. [Cài đặt FRP Client trên máy local](#cài-đặt-frp-client-trên-máy-local)
4. [Kiểm tra kết nối](#kiểm-tra-kết-nối)
5. [Thêm dịch vụ mới](#thêm-dịch-vụ-mới)
6. [Troubleshooting](#troubleshooting)
7. [Bảo mật và tối ưu hóa](#bảo-mật-và-tối-ưu-hóa)

---

## FRP là gì?

**FRP (Fast Reverse Proxy)** là một công cụ reverse proxy mã nguồn mở, được viết bằng Go, giúp expose các dịch vụ nội bộ (local services) ra Internet thông qua một VPS có IP public.

### Tại sao cần FRP?

- **Vượt qua NAT/Firewall**: Nhiều ISP không cung cấp IP public hoặc block các port
- **Truy cập từ xa**: Truy cập SSH, database, web services từ bất kỳ đâu
- **Phát triển ứng dụng**: Test webhook, API từ bên ngoài mà không cần deploy

### Các tính năng chính

- ✅ Hỗ trợ nhiều protocol: TCP, UDP, HTTP, HTTPS
- ✅ Load balancing và health check
- ✅ Dashboard web để quản lý
- ✅ Bảo mật với token và TLS encryption
- ✅ Bandwidth limiting và connection pooling
- ✅ Plugin system cho custom logic

---

## Cài đặt FRP Server trên VPS

### Yêu cầu hệ thống

- VPS/Server có IP public
- OS: Linux (Ubuntu/CentOS/Debian)
- RAM: Tối thiểu 512MB
- Disk: 100MB trống

### 1. Download và cài đặt

```bash
# Kiểm tra phiên bản mới nhất tại: https://github.com/fatedier/frp/releases
wget https://github.com/fatedier/frp/releases/download/v0.61.1/frp_0.61.1_linux_amd64.tar.gz

# Giải nén
tar -xzf frp_0.61.1_linux_amd64.tar.gz
cd frp_0.61.1_linux_amd64

# Copy binary vào system path
sudo cp frps /usr/local/bin/
sudo chmod +x /usr/local/bin/frps
```

### 2. Cấu hình cơ bản

Tạo thư mục cấu hình:

```bash
sudo mkdir -p /etc/frp
```

Tạo file cấu hình `/etc/frp/frps.toml`:

```toml
# Cấu hình server cơ bản
bindAddr = "0.0.0.0"
bindPort = 7000

# Xác thực
auth.method = "token"
auth.token = "your-secret-token-here"

# Logging
log.to = "/var/log/frp/frps.log"
log.level = "info"
log.maxDays = 7
```

### 3. Cấu hình nâng cao với Dashboard

Cho production environment, khuyến nghị sử dụng cấu hình đầy đủ:

```toml
# Server binding
bindAddr = "0.0.0.0"
bindPort = 7000

# Authentication
auth.method = "token"
auth.token = "complex-secret-token-2024"

# Web Dashboard
webServer.addr = "0.0.0.0"
webServer.port = 7500
webServer.user = "admin"
webServer.password = "secure-dashboard-password"

# TLS Configuration (recommended)
transport.tls.force = false
# transport.tls.certFile = "/etc/ssl/certs/frp.crt"
# transport.tls.keyFile = "/etc/ssl/private/frp.key"

# Logging
log.to = "/var/log/frp/frps.log"
log.level = "info"
log.maxDays = 7

# Performance tuning
transport.maxPoolCount = 5
transport.maxIdleConns = 2
transport.keepAliveInterval = 7200
transport.keepAliveTimeout = 90

# Security
allowPorts = [
  { start = 2000, end = 3000 },
  { single = 22 },
  { single = 80 },
  { single = 443 }
]
```

### 4. Tạo log directory

```bash
sudo mkdir -p /var/log/frp
sudo chown nobody:nogroup /var/log/frp
```

### 5. Tạo Systemd Service

Tạo file `/etc/systemd/system/frps.service`:

```ini
[Unit]
Description=FRP Server Service
Documentation=https://github.com/fatedier/frp
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5
ExecStart=/usr/local/bin/frps -c /etc/frp/frps.toml
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```

Khởi động service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable frps
sudo systemctl start frps

# Kiểm tra status
sudo systemctl status frps
```

### 6. Cấu hình Firewall

```bash
# UFW (Ubuntu/Debian)
sudo ufw allow 7000/tcp comment 'FRP Server'
sudo ufw allow 7500/tcp comment 'FRP Dashboard'
sudo ufw allow 2000:3000/tcp comment 'FRP Proxies'

# Firewalld (CentOS/RHEL)
sudo firewall-cmd --permanent --add-port=7000/tcp
sudo firewall-cmd --permanent --add-port=7500/tcp
sudo firewall-cmd --permanent --add-port=2000-3000/tcp
sudo firewall-cmd --reload
```

---

## Cài đặt FRP Client trên máy local

### 1. Download và cài đặt

```bash
# Download cho Linux
wget https://github.com/fatedier/frp/releases/download/v0.61.1/frp_0.61.1_linux_amd64.tar.gz
tar -xzf frp_0.61.1_linux_amd64.tar.gz

# Hoặc download cho Windows
# wget https://github.com/fatedier/frp/releases/download/v0.61.1/frp_0.61.1_windows_amd64.zip

cd frp_0.61.1_linux_amd64
sudo cp frpc /usr/local/bin/
sudo chmod +x /usr/local/bin/frpc
```

### 2. Cấu hình Client

Tạo file `/etc/frp/frpc.toml`:

```toml
# Server connection
serverAddr = "your-vps-ip-or-domain"
serverPort = 7000

# Authentication
auth.method = "token"
auth.token = "complex-secret-token-2024"

# Logging
log.to = "/var/log/frp/frpc.log"
log.level = "info"
log.maxDays = 3

# SSH Proxy
[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
remotePort = 2022

# Web Development Server
[[proxies]]
name = "web-dev"
type = "http"
localIP = "127.0.0.1"
localPort = 3000
customDomains = ["dev.yourdomain.com"]

# Database Access
[[proxies]]
name = "mysql"
type = "tcp"
localIP = "127.0.0.1"
localPort = 3306
remotePort = 2306

# RDP (Remote Desktop)
[[proxies]]
name = "rdp"
type = "tcp"
localIP = "127.0.0.1"
localPort = 3389
remotePort = 2389
```

### 3. Tạo Client Service

Tạo file `/etc/systemd/system/frpc.service`:

```ini
[Unit]
Description=FRP Client Service
Documentation=https://github.com/fatedier/frp
After=network.target

[Service]
Type=simple
User=root
Restart=on-failure
RestartSec=5
ExecStart=/usr/local/bin/frpc -c /etc/frp/frpc.toml
ExecReload=/bin/kill -s HUP $MAINPID

[Install]
WantedBy=multi-user.target
```

Khởi động client:

```bash
sudo systemctl daemon-reload
sudo systemctl enable frpc
sudo systemctl start frpc
sudo systemctl status frpc
```

---

## Kiểm tra kết nối

### 1. Kiểm tra Server status

```bash
# Kiểm tra service
sudo systemctl status frps

# Xem logs
sudo journalctl -u frps -f

# Kiểm tra port listening
sudo netstat -tlnp | grep :7000
```

### 2. Truy cập Dashboard

Mở trình duyệt và truy cập:
```
http://your-vps-ip:7500
```

Đăng nhập với username/password đã cấu hình.

### 3. Test các dịch vụ

```bash
# Test SSH
ssh -p 2022 username@your-vps-ip

# Test MySQL
mysql -h your-vps-ip -P 2306 -u username -p

# Test HTTP
curl http://dev.yourdomain.com
```

---

## Thêm dịch vụ mới

### 1. Thêm proxy mới vào client config

Chỉnh sửa `/etc/frp/frpc.toml`:

```toml
# Thêm proxy cho Redis
[[proxies]]
name = "redis"
type = "tcp"
localIP = "127.0.0.1"
localPort = 6379
remotePort = 2379

# Thêm proxy cho HTTPS web app
[[proxies]]
name = "webapp-ssl"
type = "https"
localIP = "127.0.0.1"
localPort = 8443
customDomains = ["secure.yourdomain.com"]
```

### 2. Restart client

```bash
sudo systemctl restart frpc
```

### 3. Mở port trên server

```bash
sudo ufw allow 2379/tcp comment 'Redis proxy'
```

### 4. Cấu hình DNS (cho HTTP/HTTPS)

Tạo A record trong DNS:
```
dev.yourdomain.com -> your-vps-ip
secure.yourdomain.com -> your-vps-ip
```

---

## Troubleshooting

### Các lỗi thường gặp

#### 1. Connection refused

```bash
# Kiểm tra service running
sudo systemctl status frps frpc

# Kiểm tra firewall
sudo ufw status
sudo netstat -tlnp | grep 7000
```

#### 2. Authentication failed

```bash
# Kiểm tra token trong cả server và client config
grep "auth.token" /etc/frp/frps.toml
grep "auth.token" /etc/frp/frpc.toml
```

#### 3. Port already in use

```bash
# Kiểm tra port conflicts
sudo lsof -i :7000
sudo ss -tlnp | grep 7000

# Thay đổi port trong config
```

### Debug tools

```bash
# Chạy manual để debug
/usr/local/bin/frps -c /etc/frp/frps.toml
/usr/local/bin/frpc -c /etc/frp/frpc.toml

# Monitor traffic
sudo tcpdump -i any port 7000

# Check logs
tail -f /var/log/frp/frps.log
tail -f /var/log/frp/frpc.log
```

---

## Bảo mật và tối ưu hóa

### 1. Bảo mật

#### Sử dụng TLS

```toml
# Server config
transport.tls.force = true
transport.tls.certFile = "/etc/ssl/certs/frp.crt"
transport.tls.keyFile = "/etc/ssl/private/frp.key"

# Client config
transport.tls.enable = true
transport.tls.disableCustomTLSFirstByte = false
```

#### Restrict IP access

```toml
# Chỉ cho phép client từ IP cụ thể
allowIps = ["1.2.3.4", "5.6.7.8"]
```

#### Strong token

```bash
# Generate secure token
openssl rand -base64 32
```

### 2. Monitoring

#### Setup log rotation

```bash
sudo tee /etc/logrotate.d/frp > /dev/null << EOF
/var/log/frp/*.log {
    daily
    missingok
    rotate 7
    compress
    delaycompress
    notifempty
    create 0644 nobody nogroup
    postrotate
        systemctl reload frps frpc
    endscript
}
EOF
```

#### Prometheus metrics

```toml
# Enable metrics endpoint
enablePrometheus = true
```

### 3. Performance tuning

```toml
# Connection pooling
transport.poolCount = 1
transport.maxPoolCount = 10

# Bandwidth limiting
transport.bandwidthLimit = "1MB"
transport.bandwidthLimitMode = "client"

# TCP settings
transport.tcpMux = true
transport.keepAliveInterval = 7200
```

---

## Kết luận

FRP là một công cụ mạnh mẽ và linh hoạt để expose dịch vụ nội bộ ra Internet. Với cấu hình đúng cách, bạn có thể:

- Truy cập an toàn vào các dịch vụ nội bộ từ bất kỳ đâu
- Phát triển và test ứng dụng web với domain thật
- Quản lý server/database từ xa một cách bảo mật

**Lưu ý quan trọng:**
- Luôn sử dụng token mạnh và TLS encryption
- Chỉ mở những port cần thiết
- Thường xuyên cập nhật FRP lên phiên bản mới nhất
- Monitor logs để phát hiện hoạt động bất thường

Với hướng dẫn này, bạn đã có thể triển khai FRP một cách hiệu quả và bảo mật cho nhu cầu của mình! 🚀 
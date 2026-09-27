涵盖安装、服务管理、核心配置、反向代理、负载均衡、HTTPS、性能优化及故障排查。

---

## 一、安装 Nginx

**Debian / Ubuntu：**
```bash
apt update
apt install nginx
```

**CentOS / RHEL：**
```bash
yum install epel-release
yum install nginx
```

**验证安装：**
```bash
nginx -v                 # 查看版本
nginx -V                 # 查看版本及编译参数
```

---

## 二、服务管理

**通用命令（nginx 二进制）：**
```bash
nginx                    # 启动
nginx -s reload          # 重载配置（不中断服务）
nginx -s stop            # 快速停止
nginx -s quit            # 优雅停止（处理完当前请求）
nginx -s reopen          # 重新打开日志文件（日志切割用）
nginx -t                 # 检测配置文件语法
nginx -T                 # 检测并输出完整配置
nginx -c /etc/nginx/nginx.conf   # 指定配置文件启动
nginx -p /usr/local/nginx        # 指定前缀路径
```

**systemd 管理：**
```bash
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
systemctl status nginx
systemctl enable nginx          # 开机自启
systemctl disable nginx
```

**日志位置：**
```bash
/var/log/nginx/access.log      # 访问日志
/var/log/nginx/error.log       # 错误日志
tail -f /var/log/nginx/access.log   # 实时查看访问日志
```

---

## 三、配置文件结构

**主配置文件：** `/etc/nginx/nginx.conf`

```nginx
# 全局块
user  nginx;                      # 运行用户
worker_processes  auto;           # 工作进程数（一般等于 CPU 核数）
error_log  /var/log/nginx/error.log warn;
pid        /run/nginx.pid;

# events 块
events {
    worker_connections  10240;    # 单进程最大连接数
    multi_accept on;              # 一次接受多个连接
    use epoll;                    # 使用 epoll 模型（Linux）
}

# http 块
http {
    include       mime.types;
    default_type  application/octet-stream;

    # 日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    keepalive_timeout  65;
    gzip  on;

    # 引入站点配置
    include /etc/nginx/conf.d/*.conf;
}
```

**推荐目录结构：**
```
/etc/nginx/
├── nginx.conf              # 主配置
├── conf.d/                 # 站点配置（推荐放这里）
│   ├── site1.conf
│   └── site2.conf
├── sites-enabled/          # Debian/Ubuntu 启用的站点
└── ssl/                    # 证书目录
```

---

## 四、核心配置指令

```nginx
# 性能相关
sendfile on;                 # 高效文件传输
tcp_nopush on;               # 配合 sendfile，减少网络包
tcp_nodelay on;              # 禁用 Nagle 算法，降低延迟
keepalive_timeout 65;        # 长连接超时时间
keepalive_requests 1000;     # 单个长连接最大请求数
client_max_body_size 50m;    # 最大上传体积
client_body_buffer_size 128k;

# 缓冲与超时
client_header_timeout 30;
client_body_timeout 30;
send_timeout 30;
proxy_connect_timeout 60;
proxy_send_timeout 60;
proxy_read_timeout 60;

# 压缩
gzip on;
gzip_min_length 1k;
gzip_comp_level 4;
gzip_types text/plain text/css application/json application/javascript text/xml;
gzip_vary on;
gzip_disable "MSIE [1-6]\.";
```

---

## 五、虚拟主机（server 块）

```nginx
# 基于域名
server {
    listen       80;
    server_name  www.example.com example.com;

    root   /var/www/example;
    index  index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}

# 基于端口
server {
    listen       8080;
    server_name  _;
    root /var/www/app;
}

# 默认站点（兜底）
server {
    listen       80 default_server;
    server_name  _;
    return 444;               # 直接断开连接
}
```

---

## 六、location 匹配规则

```nginx
# 精确匹配（优先级最高）
location = /login {
    # 仅匹配 /login
}

# 前缀匹配（^~ 表示不再检查正则）
location ^~ /static/ {
    root /var/www;
}

# 正则匹配（~ 区分大小写，~* 不区分）
location ~* \.(jpg|png|gif|css|js)$ {
    expires 30d;
}

# 普通前缀匹配
location / {
    proxy_pass http://backend;
}

# 优先级：= > ^~ > ~ / ~* > 普通前缀
```

---

## 七、反向代理

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;

        # 传递真实信息给后端
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 支持
        proxy_http_version 1.1;
        proxy_set_header Upgrade    $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

**多路径代理：**
```nginx
location /api/ {
    proxy_pass http://127.0.0.1:8080/;
}
location /web/ {
    proxy_pass http://127.0.0.1:3000/;
}
location / {
    proxy_pass http://127.0.0.1:5000;
}
```

---

## 八、负载均衡

```nginx
http {
    upstream backend {
        # 负载均衡策略
        # 默认轮询（round robin）
        # ip_hash;             # 按 IP 哈希，会话保持
        # least_conn;          # 最少连接数

        server 192.168.1.10:8080 weight=5;   # 权重
        server 192.168.1.11:8080 weight=3;
        server 192.168.1.12:8080 backup;     # 备用服务器
        server 192.168.1.13:8080 down;       # 标记下线

        keepalive 32;          # 保持长连接数
    }

    server {
        listen 80;
        server_name www.example.com;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

---

## 九、HTTPS 配置

```nginx
server {
    listen       443 ssl http2;
    server_name  www.example.com;

    # 证书配置
    ssl_certificate      /etc/nginx/ssl/example.com.pem;
    ssl_certificate_key  /etc/nginx/ssl/example.com.key;

    # SSL 优化
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # HSTS（强制 HTTPS）
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    root /var/www/example;
    location / {
        try_files $uri $uri/ =404;
    }
}

# HTTP 强制跳转 HTTPS
server {
    listen       80;
    server_name  www.example.com example.com;
    return 301 https://$host$request_uri;
}
```

**生成自签名证书（测试用）：**
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/self.key \
  -out /etc/nginx/ssl/self.crt
```

**免费证书（Let's Encrypt / certbot）：**
```bash
apt install certbot python3-certbot-nginx
certbot --nginx -d example.com -d www.example.com
```

---

## 十、静态资源与缓存

```nginx
server {
    listen 80;
    server_name static.example.com;
    root /var/www/static;

    # 静态资源缓存
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff2)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
        access_log off;
    }

    # 禁止访问隐藏文件
    location ~ /\. {
        deny all;
    }

    # 目录列表（一般关闭）
    autoindex off;
}
```

**文件切片/断点续传：**
```nginx
location /download/ {
    root /var/www;
    max_ranges 1;          # 支持 Range 请求
}
```

---

## 十一、URL 重写与跳转

```nginx
# 301 永久重定向
location /old {
    return 301 https://$host/new;
}

# 302 临时重定向
location /promo {
    return 302 https://$host/landing;
}

# rewrite 重写（末尾 last 重新匹配，break 不再匹配）
location /blog {
    rewrite ^/blog/(.*)$ /articles/$1 last;
}

# 强制 HTTPS（方法二）
if ($scheme = http) {
    return 301 https://$host$request_uri;
}

# 去掉 www
if ($host = 'www.example.com') {
    return 301 https://example.com$request_uri;
}

# 禁止 IP 直接访问
if ($host !~* ^example\.com$) {
    return 444;
}
```

---

## 十二、访问控制

```nginx
# IP 黑白名单
location /admin {
    allow 192.168.1.0/24;
    allow 127.0.0.1;
    deny all;
}

# 密码认证（HTTP Basic Auth）
location /secure {
    auth_basic "Restricted Area";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
# 生成密码文件：
# apt install apache2-utils
# htpasswd -c /etc/nginx/.htpasswd username

# 限制请求速率（防刷）
limit_req_zone $binary_remote_addr zone=login:10m rate=5r/m;

location /login {
    limit_req zone=login burst=5 nodelay;
    proxy_pass http://backend;
}

# 限制并发连接数
limit_conn_zone $binary_remote_addr zone=addr:10m;

location /download/ {
    limit_conn addr 10;     # 单 IP 最多 10 个并发
}

# 禁止特定 User-Agent
if ($http_user_agent ~* (curl|wget|python)) {
    return 403;
}
```

---

## 十三、日志配置

```nginx
http {
    # 自定义日志格式
    log_format  json  escape=json '{"time":"$time_iso8601",'
                      '"remote_addr":"$remote_addr",'
                      '"request":"$request",'
                      '"status":"$status",'
                      '"body_bytes_sent":"$body_bytes_sent",'
                      '"request_time":"$request_time",'
                      '"upstream_response_time":"$upstream_response_time"}';

    server {
        listen 80;
        server_name example.com;

        access_log /var/log/nginx/example_access.log json;
        error_log  /var/log/nginx/example_error.log warn;
    }
}
```

**日志切割（logrotate 配置）：**
```
# /etc/logrotate.d/nginx
/var/log/nginx/*.log {
    daily
    missingok
    rotate 30
    compress
    notifempty
    create 0640 nginx adm
    sharedscripts
    postrotate
        [ -f /var/run/nginx.pid ] && kill -USR1 $(cat /var/run/nginx.pid)
    endscript
}
```

---

## 十四、性能优化

```nginx
# 全局优化
worker_processes  auto;                # 一般设为 CPU 核数
worker_cpu_affinity auto;              # 绑定 CPU 核心
worker_rlimit_nofile 65535;            # 最大文件描述符

events {
    worker_connections  10240;         # 单进程连接数
    use epoll;
    multi_accept on;
}

http {
    # 开启高效传输
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;

    # 长连接
    keepalive_timeout  65;
    keepalive_requests 1000;

    # 文件缓存
    open_file_cache max=10000 inactive=20s;
    open_file_cache_valid 30s;
    open_file_cache_min_uses 2;
    open_file_cache_errors on;

    # FastCGI 缓存（PHP 等场景）
    fastcgi_cache_path /tmp/nginx_cache levels=1:2 keys_zone=fcgi:100m inactive=60m;
    fastcgi_cache_key "$scheme$request_method$host$request_uri";

    # Gzip 压缩
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 4;
    gzip_min_length 1k;
    gzip_types text/plain text/css application/json application/javascript image/svg+xml;
}
```

**系统层配套优化（/etc/sysctl.conf）：**
```bash
net.core.somaxconn = 65535
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_fin_timeout = 15
# 执行 sysctl -p 生效
```

---

## 十五、健康检查与状态监控

```nginx
# Nginx 状态页（需 ngx_http_stub_status_module）
location /nginx_status {
    stub_status on;
    allow 127.0.0.1;
    deny all;
}
```

**upstream 健康检查参数：**
```nginx
upstream backend {
    server 192.168.1.10:8080 max_fails=3 fail_timeout=30s;
    server 192.168.1.11:8080 max_fails=3 fail_timeout=30s;
}
```

---

## 十六、故障排查常用命令

```bash
nginx -t                          # 检查配置语法
nginx -T                          # 输出完整生效配置
nginx -V 2>&1 | grep -o with-.*   # 查看编译模块

tail -f /var/log/nginx/error.log  # 查看错误日志
tail -f /var/log/nginx/access.log # 查看访问日志

ss -tunlp | grep nginx            # 查看 Nginx 监听端口
ps -ef | grep nginx               # 查看 Nginx 进程
netstat -tunlp | grep :80         # 查看 80 端口占用

curl -I http://localhost          # 测试本地访问
curl -H "Host: example.com" http://127.0.0.1   # 模拟域名访问

# 常见错误排查
# 1. 403 Forbidden  → 检查目录权限、SELinux、index 文件
# 2. 502 Bad Gateway → 后端服务未启动或端口错误
# 3. 504 Gateway Timeout → 调大 proxy_read_timeout
# 4. 413 Request Entity Too Large → 调大 client_max_body_size
# 5. worker_connections are not enough → 调大 worker_connections
```

**权限排查（常见坑）：**
```bash
ps aux | grep nginx              # 确认运行用户（nginx.conf 中的 user）
chown -R nginx:nginx /var/www    # 修改网站目录归属
chmod -R 755 /var/www            # 修改目录权限
getsebool -a | grep httpd        # SELinux 检查（CentOS）
setsebool -P httpd_can_network_connect 1   # 允许 Nginx 反向代理
```

---

## 十七、完整站点配置示例

```nginx
upstream app_backend {
    least_conn;
    server 127.0.0.1:8080 weight=5;
    server 127.0.0.1:8081 weight=3;
    keepalive 32;
}

server {
    listen       80;
    listen       443 ssl http2;
    server_name  www.example.com example.com;

    ssl_certificate      /etc/nginx/ssl/example.com.pem;
    ssl_certificate_key  /etc/nginx/ssl/example.com.key;
    ssl_protocols        TLSv1.2 TLSv1.3;

    root   /var/www/example;
    index  index.html;

    client_max_body_size 50m;

    # 静态资源
    location ~* \.(jpg|png|css|js|woff2)$ {
        expires 30d;
        access_log off;
    }

    # API 反向代理
    location /api/ {
        proxy_pass http://app_backend/;
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # 前端路由
    location / {
        try_files $uri $uri/ /index.html;
    }

    # 错误页面
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```
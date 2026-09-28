涵盖安装、服务管理、核心配置、虚拟主机、反向代理、HTTPS、性能优化及故障排查。

---

## 一、安装 Apache

**Debian / Ubuntu（Apache 服务名为 `apache2`）：**
```bash
apt update
apt install apache2
```

**CentOS / RHEL（Apache 服务名为 `httpd`）：**
```bash
yum install httpd
```

**验证安装：**
```bash
apache2 -v          # Debian/Ubuntu 查看版本
httpd -v            # CentOS/RHEL 查看版本
apachectl -v        # 通用（若已安装）
```

> ⚠️ 后文命令中，Debian/Ubuntu 用 `apache2`，CentOS/RHEL 用 `httpd`，请按发行版替换。

---

## 二、服务管理

**systemd 管理：**
```bash
# Debian/Ubuntu
systemctl start apache2
systemctl stop apache2
systemctl restart apache2
systemctl reload apache2
systemctl status apache2
systemctl enable apache2          # 开机自启
systemctl disable apache2

# CentOS/RHEL
systemctl start httpd
systemctl stop httpd
systemctl restart httpd
systemctl reload httpd
systemctl status httpd
systemctl enable httpd
systemctl disable httpd
```

**apachectl 控制命令：**
```bash
apachectl start            # 启动
apachectl stop             # 停止
apachectl restart          # 重启
apachectl graceful         # 优雅重启（不断开连接）
apachectl configtest       # 检测配置语法（重要！）
apachectl -t               # 同上（简写）
apachectl -M               # 列出已加载模块
apachectl -S               # 显示虚拟主机配置摘要
apachectl -V               # 查看编译参数
apachectl -l               # 列出静态编译模块
```

**日志位置：**
```bash
# Debian/Ubuntu
/var/log/apache2/access.log
/var/log/apache2/error.log

# CentOS/RHEL
/var/log/httpd/access_log
/var/log/httpd/error_log

tail -f /var/log/apache2/access.log   # 实时查看访问日志
```

---

## 三、配置文件结构

**主配置文件：**
```
# Debian/Ubuntu
/etc/apache2/apache2.conf
/etc/apache2/ports.conf
/etc/apache2/sites-available/       # 站点配置（可用）
/etc/apache2/sites-enabled/         # 站点配置（已启用，软链接）
/etc/apache2/mods-available/        # 模块配置（可用）
/etc/apache2/mods-enabled/          # 模块配置（已启用）
/etc/apache2/conf-available/        # 全局配置（可用）
/etc/apache2/conf-enabled/          # 全局配置（已启用）

# CentOS/RHEL
/etc/httpd/conf/httpd.conf          # 主配置
/etc/httpd/conf.d/                  # 附加配置目录
/etc/httpd/conf.modules.d/          # 模块配置
```

**apache2.conf 核心结构：**
```apache
# 全局配置
ServerRoot "/etc/apache2"
PidFile ${APACHE_PID_FILE}
Timeout 300
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5

# 运行用户
User www-data
Group www-data

# 加载模块
Include mods-enabled/*.load
Include mods-enabled/*.conf

# 加载站点
Include sites-enabled/*.conf
```

---

## 四、核心配置指令

```apache
# 基本全局参数
ServerRoot "/etc/apache2"        # 配置根目录
ServerName www.example.com:80    # 服务器域名
ServerAdmin admin@example.com    # 管理员邮箱
Timeout 300                      # 超时时间（秒）
Listen 80                        # 监听端口
Listen 443 https                 # 监听 HTTPS

# 连接管理
KeepAlive On                     # 开启长连接
MaxKeepAliveRequests 100         # 单连接最大请求数
KeepAliveTimeout 5               # 长连接超时

# 配置目录权限
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

# 拒绝访问隐藏文件
<FilesMatch "^\.ht">
    Require all denied
</FilesMatch>
```

---

## 五、虚拟主机（VirtualHost）

**基于域名：**
```apache
<VirtualHost *:80>
    ServerName www.example.com
    ServerAlias example.com
    DocumentRoot /var/www/example

    <Directory "/var/www/example">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog  ${APACHE_LOG_DIR}/example_error.log
    CustomLog ${APACHE_LOG_DIR}/example_access.log combined
</VirtualHost>
```

**基于端口：**
```apache
Listen 8080

<VirtualHost *:8080>
    ServerName app.example.com
    DocumentRoot /var/www/app
</VirtualHost>
```

**默认虚拟主机（兜底）：**
```apache
<VirtualHost *:80>
    ServerName _default_
    DocumentRoot /var/www/default
</VirtualHost>
```

**Debian/Ubuntu 启用站点：**
```bash
a2ensite example.com.conf       # 启用站点
a2dissite example.com.conf      # 禁用站点
a2enmod rewrite                 # 启用模块
a2dismod rewrite                # 禁用模块
a2enconf security               # 启用配置
systemctl reload apache2
```

---

## 六、URL 重写（mod_rewrite）

```apache
# 启用模块（Debian/Ubuntu）
a2enmod rewrite

<VirtualHost *:80>
    ServerName www.example.com
    DocumentRoot /var/www/example

    <Directory "/var/www/example">
        AllowOverride All          # 允许 .htaccess 生效
        Options FollowSymLinks
    </Directory>

    # 开启重写
    RewriteEngine On

    # 强制 HTTPS
    RewriteCond %{HTTPS} !=on
    RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

    # 去掉 www
    RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
    RewriteRule ^(.*)$ https://%1/$1 [R=301,L]

    # 前端路由（SPA 项目）
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ /index.html [L]

    # URL 重写
    RewriteRule ^blog/(.*)$ /articles/$1 [L]
</VirtualHost>
```

---

## 七、反向代理（mod_proxy）

**启用模块：**
```bash
a2enmod proxy proxy_http proxy_balancer proxy_hcheck lbmethod_byrequests
# Debian/Ubuntu
systemctl reload apache2
```

**基本反向代理：**
```apache
<VirtualHost *:80>
    ServerName api.example.com

    ProxyPreserveHost On
    ProxyPass        / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/

    # 传递真实 IP
    ProxyPassReverse / http://127.0.0.1:8080/
    RequestHeader set X-Real-IP "%{REMOTE_ADDR}s"
    RequestHeader set X-Forwarded-For "%{REMOTE_ADDR}s"
</VirtualHost>
```

**多路径代理：**
```apache
ProxyPass        /api/ http://127.0.0.1:8080/
ProxyPassReverse /api/ http://127.0.0.1:8080/

ProxyPass        /web/ http://127.0.0.1:3000/
ProxyPassReverse /web/ http://127.0.0.1:3000/
```

**排除某些路径（不代理）：**
```apache
ProxyPass /static/ !
ProxyPass / http://127.0.0.1:8080/
```

**WebSocket 代理：**
```apache
RewriteEngine On
RewriteCond %{HTTP:Upgrade} websocket [NC]
RewriteCond %{HTTP:Connection} upgrade [NC]
RewriteRule /(.*) ws://127.0.0.1:8080/$1 [P,L]

ProxyPass        /ws/ ws://127.0.0.1:8080/
ProxyPassReverse /ws/ ws://127.0.0.1:8080/
```

---

## 八、负载均衡（mod_proxy_balancer）

```apache
<Proxy "balancer://mycluster">
    BalancerMember http://192.168.1.10:8080 loadfactor=5
    BalancerMember http://192.168.1.11:8080 loadfactor=3
    BalancerMember http://192.168.1.12:8080 status=+H     # 备用
    ProxySet lbmethod=byrequests
    # 策略：byrequests（轮询）、bytraffic（流量）、bybusyness（忙碌）
</Proxy>

<VirtualHost *:80>
    ServerName www.example.com

    ProxyPreserveHost On
    ProxyPass        / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    # 管理页面（监控节点状态）
    <Location /balancer-manager>
        SetHandler balancer-manager
        Require local
    </Location>
</VirtualHost>
```

**会话保持（粘性会话）：**
```apache
Header add Set-Cookie "ROUTEID=.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED

<Proxy "balancer://mycluster">
    BalancerMember http://192.168.1.10:8080 route=1
    BalancerMember http://192.168.1.11:8080 route=2
    ProxySet stickysession=ROUTEID
</Proxy>
```

---

## 九、HTTPS / SSL 配置

**启用模块：**
```bash
a2enmod ssl          # Debian/Ubuntu
a2ensite default-ssl.conf
systemctl reload apache2
```

**虚拟主机配置：**
```apache
<VirtualHost *:443>
    ServerName www.example.com
    DocumentRoot /var/www/example

    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/example.com.pem
    SSLCertificateKeyFile /etc/ssl/private/example.com.key
    SSLCertificateChainFile /etc/ssl/certs/chain.pem

    # SSL 优化
    SSLProtocol             all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite          HIGH:!aNULL:!MD5
    SSLHonorCipherOrder     on
    SSLSessionCache         shmcb:/var/run/ssl_scache(512000)
    SSLSessionCacheTimeout  300

    # HSTS（强制 HTTPS）
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</VirtualHost>

# HTTP 跳转 HTTPS
<VirtualHost *:80>
    ServerName www.example.com
    Redirect permanent / https://www.example.com/
</VirtualHost>
```

**生成自签名证书（测试用）：**
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/self.key \
  -out /etc/ssl/certs/self.crt
```

**免费证书（Let's Encrypt / certbot）：**
```bash
apt install certbot python3-certbot-apache
certbot --apache -d example.com -d www.example.com
```

---

## 十、.htaccess 常用配置

> `.htaccess` 放在站点根目录，需 `AllowOverride All` 才生效。

```apache
# 开启重写
RewriteEngine On

# 强制 HTTPS
RewriteCond %{HTTPS} !=on
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

# 禁止访问 .ht 文件
<FilesMatch "^\.ht">
    Order allow,deny
    Deny from all
</FilesMatch>

# 目录密码保护
AuthType Basic
AuthName "Restricted Area"
AuthUserFile /var/www/example/.htpasswd
Require valid-user
# 生成密码：
# htpasswd -c /var/www/example/.htpasswd username

# 禁止 IP 访问
Order deny,allow
Deny from 192.168.1.100
Allow from all

# 自定义错误页
ErrorDocument 404 /404.html
ErrorDocument 500 /50x.html

# 禁止目录列表
Options -Indexes

# 设置默认首页
DirectoryIndex index.html index.php

# URL 重写（去 .html 后缀）
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME}\.html -f
RewriteRule ^(.*)$ $1.html [L]
```

---

## 十一、访问控制与认证

**IP 黑白名单：**
```apache
<Directory "/var/www/admin">
    Require ip 192.168.1.0/24
    Require ip 127.0.0.1
    # Require all denied
</Directory>
```

**HTTP Basic 认证：**
```apache
<Directory "/var/www/secure">
    AuthType Basic
    AuthName "Restricted Area"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```

**生成密码文件：**
```bash
# Debian/Ubuntu
htpasswd -c /etc/apache2/.htpasswd username

# CentOS/RHEL
htpasswd -c /etc/httpd/.htpasswd username
```

**限制请求速率（mod_ratelimit）：**
```bash
a2enmod ratelimit
```
```apache
<Directory "/var/www/download">
    SetOutputFilter RATE_LIMIT
    SetEnv rate-limit 512       # KB/s
</Directory>
```

---

## 十二、日志配置

```apache
# 自定义日志格式
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %D" detailed

<VirtualHost *:80>
    ServerName www.example.com

    ErrorLog  ${APACHE_LOG_DIR}/example_error.log
    CustomLog ${APACHE_LOG_DIR}/example_access.log detailed

    # 不记录静态资源日志
    SetEnvIf Request_URI "\.(jpg|png|css|js)$" dontlog
    CustomLog ${APACHE_LOG_DIR}/example_access.log detailed env=!dontlog
</VirtualHost>
```

**常见日志变量：**
| 变量 | 说明 |
|------|------|
| `%h` | 客户端 IP |
| `%t` | 时间 |
| `%r` | 请求行 |
| `%>s` | 响应状态码 |
| `%b` | 发送字节数 |
| `%D` | 处理时间（微秒） |
| `%{Referer}i` | 来源页面 |
| `%{User-Agent}i` | 浏览器标识 |

**日志切割（logrotate）：**
```
# /etc/logrotate.d/apache2
/var/log/apache2/*.log {
    daily
    missingok
    rotate 30
    compress
    notifempty
    create 0640 www-data adm
    sharedscripts
    postrotate
        systemctl reload apache2 > /dev/null 2>&1 || true
    endscript
}
```

---

## 十三、性能优化

```apache
# 进程管理（prefork / event / worker）
# 推荐 event（Apache 2.4 默认）
<IfModule mpm_event_module>
    StartServers             3
    MinSpareThreads          75
    MaxSpareThreads          250
    ThreadLimit              64
    ThreadsPerChild          25
    MaxRequestWorkers        400
    MaxConnectionsPerChild   0
</IfModule>

# 老版本 prefork（PHP 场景）
<IfModule mpm_prefork_module>
    StartServers          5
    MinSpareServers       5
    MaxSpareServers       10
    MaxRequestWorkers     150
    MaxConnectionsPerChild 1000
</IfModule>

# 连接优化
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5
Timeout 60

# 开启压缩（mod_deflate）
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/css
    AddOutputFilterByType DEFLATE application/json application/javascript
    AddOutputFilterByType DEFLATE image/svg+xml
</IfModule>

# 浏览器缓存（mod_expires）
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpeg "access plus 30 days"
    ExpiresByType image/png  "access plus 30 days"
    ExpiresByType text/css   "access plus 7 days"
    ExpiresByType application/javascript "access plus 7 days"
</IfModule>

# 开启 sendfile
EnableSendfile On
EnableMMAP On
```

**常用优化模块启用：**
```bash
a2enmod deflate expires headers rewrite ssl proxy proxy_http
systemctl reload apache2
```

---

## 十四、常用模块速查

| 模块 | 说明 | 启用命令 |
|------|------|----------|
| `mod_rewrite` | URL 重写 | `a2enmod rewrite` |
| `mod_proxy` | 反向代理 | `a2enmod proxy` |
| `mod_proxy_http` | HTTP 代理 | `a2enmod proxy_http` |
| `mod_proxy_balancer` | 负载均衡 | `a2enmod proxy_balancer` |
| `mod_ssl` | HTTPS 支持 | `a2enmod ssl` |
| `mod_deflate` | Gzip 压缩 | `a2enmod deflate` |
| `mod_expires` | 浏览器缓存 | `a2enmod expires` |
| `mod_headers` | HTTP 头操作 | `a2enmod headers` |
| `mod_alias` | URL 别名/重定向 | `a2enmod alias` |
| `mod_mime` | MIME 类型 | 默认启用 |
| `mod_status` | 状态监控 | `a2enmod status` |
| `mod_info` | 服务器信息 | `a2enmod info` |
| `mod_ratelimit` | 限速 | `a2enmod ratelimit` |
| `mod_cache` | 缓存 | `a2enmod cache cache_disk` |

---

## 十五、状态监控

```apache
# 服务器状态页
<Location /server-status>
    SetHandler server-status
    Require local
    # Require ip 192.168.1.0/24
</Location>

# 服务器信息页
<Location /server-info>
    SetHandler server-info
    Require local
</Location>

# Balancer 管理页（负载均衡监控）
<Location /balancer-manager>
    SetHandler balancer-manager
    Require local
</Location>
```

访问：`http://localhost/server-status`、`http://localhost/server-info`

---

## 十六、故障排查常用命令

```bash
# 配置检查
apachectl configtest            # 检查语法（强烈推荐每次改配置后执行）
apachectl -t                    # 同上
apachectl -S                    # 查看虚拟主机配置摘要
apachectl -M                    # 查看已加载模块
apachectl -V                    # 查看编译参数
apachectl -l                    # 静态编译模块

# 日志查看
tail -f /var/log/apache2/error.log
tail -f /var/log/apache2/access.log
tail -100 /var/log/apache2/error.log

# 进程与端口
ps -ef | grep apache
ps -ef | grep httpd
ss -tunlp | grep :80
netstat -tunlp | grep :80
lsof -i:80

# 访问测试
curl -I http://localhost
curl -H "Host: example.com" http://127.0.0.1
curl -k https://localhost        # 忽略证书测试

# 权限排查
ls -la /var/www/                 # 查看目录权限
ps aux | grep apache             # 确认运行用户
chown -R www-data:www-data /var/www/    # Debian/Ubuntu
chown -R apache:apache /var/www/        # CentOS/RHEL
chmod -R 755 /var/www/

# SELinux（CentOS 常见问题）
getsebool -a | grep httpd
setsebool -P httpd_can_network_connect 1
setsebool -P httpd_enable_homedirs 1
chcon -R -t httpd_sys_content_t /var/www/

# 常见错误
# 1. 403 Forbidden   → 目录权限 / SELinux / Options 缺少 Indexes
# 2. 404 Not Found   → DocumentRoot 路径错误
# 3. 500 Internal    → 查看 error.log，多为 .htaccess 语法错误
# 4. 502 Bad Gateway → 后端服务未启动
# 5. AH00558         → 未设置 ServerName（加上即可）
# 6. Address already in use → 端口被占用
```

---

## 十七、完整站点配置示例

```apache
# /etc/apache2/sites-available/example.com.conf

# 负载均衡后端
<Proxy "balancer://app_cluster">
    BalancerMember http://127.0.0.1:8080 loadfactor=5
    BalancerMember http://127.0.0.1:8081 loadfactor=3
    ProxySet lbmethod=byrequests
</Proxy>

# HTTP → HTTPS 跳转
<VirtualHost *:80>
    ServerName www.example.com
    ServerAlias example.com
    Redirect permanent / https://www.example.com/
</VirtualHost>

# HTTPS 主站
<VirtualHost *:443>
    ServerName www.example.com
    ServerAlias example.com
    DocumentRoot /var/www/example

    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/example.com.pem
    SSLCertificateKeyFile /etc/ssl/private/example.com.key
    SSLProtocol           all -SSLv3 -TLSv1 -TLSv1.1

    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "SAMEORIGIN"

    <Directory "/var/www/example">
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    # 静态资源缓存
    <FilesMatch "\.(jpg|jpeg|png|gif|ico|css|js|woff2)$">
        ExpiresActive On
        ExpiresByType image/jpeg "access plus 30 days"
        ExpiresByType text/css   "access plus 7 days"
    </FilesMatch>

    # API 反向代理
    ProxyPreserveHost On
    ProxyPass        /api/ balancer://app_cluster/
    ProxyPassReverse /api/ balancer://app_cluster/

    # 前端路由（SPA）
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ /index.html [L]

    # 日志
    ErrorLog  ${APACHE_LOG_DIR}/example_error.log
    CustomLog ${APACHE_LOG_DIR}/example_access.log combined
</VirtualHost>
```

**启用站点：**
```bash
a2ensite example.com.conf
a2enmod rewrite ssl proxy proxy_http proxy_balancer headers expires
apachectl configtest
systemctl reload apache2
```
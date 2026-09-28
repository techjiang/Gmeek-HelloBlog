> 全面涵盖安装部署、后台管理、主题开发、插件开发、性能优化、安全加固、SEO 优化、多站点管理、API 二次开发及故障排查。

## 一、WordPress 简介

### 什么是 WordPress

WordPress 是全球最流行的开源内容管理系统（CMS），占据全球网站 **43%+** 的市场份额。它以易用性、丰富的主题/插件生态和强大的扩展能力著称。

### 核心特点

| 特点 | 说明 |
|------|------|
| **免费开源** | 基于 GPL 协议，完全免费 |
| **易用性** | 可视化编辑，零代码建站 |
| **主题系统** | 数万款免费/付费主题 |
| **插件生态** | 60,000+ 免费插件 |
| **多语言** | 支持 200+ 种语言 |
| **SEO 友好** | 天然对搜索引擎友好 |
| **社区活跃** | 全球最大 CMS 社区 |
| **开发者友好** | 丰富的 API、钩子系统 |

### WordPress.org vs WordPress.com

| 特性 | WordPress.org（自建） | WordPress.com（托管） |
|------|----------------------|----------------------|
| 费用 | 免费（需主机/域名） | 免费/付费方案 |
| 自定义 | 完全自由 | 有限制 |
| 插件 | 任意安装 | 仅付费方案 |
| 主题 | 任意安装 | 有限制 |
| 代码 | 完全可修改 | 不可修改 |
| 广告 | 无强制 | 免费版有广告 |
| 适合 | 开发者/企业 | 个人博客/新手 |

### 适用场景

- 个人博客 / 内容网站
- 企业官网 / 品牌展示
- 电子商务（WooCommerce）
- 论坛社区（BuddyPress / bbPress）
- 在线课程（LearnDash / Tutor LMS）
- 新闻 / 杂志网站
- 作品集 / 个人主页

### 官方资源

- 官网：https://wordpress.org
- 文档（Codex）：https://developer.wordpress.org
- 主题市场：https://wordpress.org/themes
- 插件市场：https://wordpress.org/plugins
- 社区论坛：https://wordpress.org/support
- Gutenberg 编辑器：https://wordpress.org/gutenberg

---

## 二、环境准备

### 2.1 服务器要求

| 组件 | 最低要求 | 推荐配置 |
|------|----------|----------|
| PHP | 7.4+ | 8.1+ |
| MySQL | 5.7+ / MariaDB 10.3+ | MySQL 8.0+ / MariaDB 10.6+ |
| Web 服务器 | Nginx / Apache | Nginx（性能更好） |
| 内存 | 512MB | 2GB+ |
| 磁盘 | 1GB | 10GB+ |
| HTTPS | 推荐 | 必须（Let's Encrypt 免费） |

### 2.2 宝塔面板（新手推荐）

```bash
# 安装宝塔面板（CentOS）
yum install -y wget && wget -O install.sh https://download.bt.cn/install/install_lts.sh && sh install.sh

# 安装宝塔面板（Ubuntu/Debian）
wget -O install.sh https://download.bt.cn/install/install_lts.sh && sudo bash install.sh

# 登录面板
# 安装完成后会显示面板地址和账号密码

# 在面板中安装：
# Nginx 1.24+、MySQL 8.0+、PHP 8.1+
# 一键部署 WordPress
```

### 2.3 手动安装 LNMP（Nginx + PHP + MySQL）

**Ubuntu/Debian：**
```bash
# Nginx
sudo apt install -y nginx

# PHP 8.1+
sudo apt install -y php8.1-fpm php8.1-mysql php8.1-curl php8.1-gd php8.1-mbstring php8.1-xml php8.1-zip php8.1-intl php8.1-bcmath

# MySQL
sudo apt install -y mysql-server
sudo mysql_secure_installation

# 启动
sudo systemctl enable nginx php8.1-fpm mysql
sudo systemctl start nginx php8.1-fpm mysql
```

**CentOS/Rocky/Alma：**
```bash
# EPEL + Remi 源（获取新版 PHP）
sudo yum install -y epel-release
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo yum module reset php
sudo yum module enable php:remi-8.1

# Nginx
sudo yum install -y nginx

# PHP
sudo yum install -y php-fpm php-mysqlnd php-curl php-gd php-mbstring php-xml php-zip php-intl php-bcmath php-opcache

# MySQL
sudo yum install -y mysql-server
sudo mysqld --initialize-insecure
sudo systemctl enable mysqld
sudo systemctl start mysqld

# 启动
sudo systemctl enable nginx php-fpm
sudo systemctl start nginx php-fpm
```

### 2.4 LAMP（Apache + PHP + MySQL）

**Ubuntu/Debian：**
```bash
sudo apt install -y apache2 php php-mysql php-curl php-gd php-mbstring php-xml php-zip libapache2-mod-php mysql-server

sudo systemctl enable apache2 mysql
sudo systemctl start apache2 mysql

# 启用 rewrite 模块（WordPress 固定链接需要）
sudo a2enmod rewrite
sudo systemctl reload apache2
```

**CentOS/Rocky/Alma：**
```bash
sudo yum install -y httpd php php-mysqlnd php-curl php-gd php-mbstring php-xml php-zip

sudo yum install -y mysql-server
sudo systemctl enable httpd mysqld
sudo systemctl start httpd mysqld
```

### 2.5 创建数据库

```bash
# 登录 MySQL
sudo mysql -u root -p

# 创建 WordPress 数据库
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'YourStrongPassword123!';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# 验证
mysql -u wpuser -p'YourStrongPassword123!' -e "SHOW DATABASES;"
```

---

## 三、安装部署

### 3.1 下载 WordPress

```bash
# 方式一：官方下载
cd /var/www
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzf latest.tar.gz
sudo chown -R www-data:www-data wordpress   # Debian/Ubuntu
# sudo chown -R nginx:nginx wordpress        # CentOS/Nginx
# sudo chown -R apache:apache wordpress      # CentOS/Apache

# 方式二：使用 WP-CLI（推荐）
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp

# 使用 WP-CLI 安装
cd /var/www
sudo wp core download --locale=zh_CN
```

### 3.2 配置 Nginx

```bash
sudo nano /etc/nginx/sites-available/wordpress
```

**Nginx 配置：**
```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/wordpress;
    index index.php index.html;

    client_max_body_size 64M;

    # 固定链接重写（WordPress 核心）
    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    # PHP 处理
    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;    # Debian/Ubuntu
        # fastcgi_pass 127.0.0.1:9000;                 # CentOS 或自定义
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_read_timeout 300;
    }

    # 禁止访问敏感文件
    location ~* /\.ht {
        deny all;
    }
    location = /wp-config.php {
        deny all;
    }
    location = /xmlrpc.php {
        deny all;        # 禁用 XML-RPC（防攻击）
    }

    # 静态资源缓存
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff2|svg)$ {
        expires 30d;
        access_log off;
        add_header Cache-Control "public, immutable";
    }

    # Gzip 压缩
    gzip on;
    gzip_vary on;
    gzip_min_length 1k;
    gzip_comp_level 4;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml image/svg+xml;

    # 禁止目录浏览
    autoindex off;

    # 日志
    access_log /var/log/nginx/wordpress_access.log;
    error_log /var/log/nginx/wordpress_error.log;
}
```

```bash
# 启用站点
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### 3.3 配置 Apache

```bash
sudo nano /etc/apache2/sites-available/wordpress.conf
```

**Apache 配置：**
```apache
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/wordpress

    <Directory /var/www/wordpress>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/wordpress_error.log
    CustomLog ${APACHE_LOG_DIR}/wordpress_access.log combined
</VirtualHost>
```

```bash
sudo a2ensite wordpress.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

### 3.4 配置 wp-config.php

```bash
cd /var/www/wordpress
cp wp-config-sample.php wp-config.php
nano wp-config.php
```

```php
<?php
// 数据库配置
define('DB_NAME', 'wordpress');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'YourStrongPassword123!');
define('DB_HOST', 'localhost');
define('DB_CHARSET', 'utf8mb4');
define('DB_COLLATE', 'utf8mb4_unicode_ci');

// 直接粘贴官方生成的密钥
// 访问 https://api.wordpress.org/secret-key/1.1/salt/ 生成

// WordPress 表前缀
$table_prefix = 'wp_';

// 调试模式（生产环境设为 false）
define('WP_DEBUG', false);
define('WP_DEBUG_LOG', false);
define('WP_DEBUG_DISPLAY', false);

// 内存限制
define('WP_MEMORY_LIMIT', '256M');
define('WP_MAX_MEMORY_LIMIT', '512M');

// 文件编辑器（生产环境禁用）
define('DISALLOW_FILE_EDIT', true);

// 自动更新
define('WP_AUTO_UPDATE_CORE', 'minor');

// 文件系统方法
define('FS_METHOD', 'direct');

if ( ! defined( 'ABSPATH' ) ) {
    define( 'ABSPATH', __DIR__ . '/' );
}
require_once ABSPATH . 'wp-settings.php';
```

### 3.5 Web 安装向导

```
1. 浏览器访问 http://your-domain.com
2. 选择语言：简体中文
3. 填写站点信息：
   - 站点标题
   - 用户名（admin 或自定义，避免用 admin）
   - 密码（强密码）
   - 电子邮件
   - 搜索引擎可见性（开发时勾选"阻止搜索引擎"）
4. 点击"安装 WordPress"
5. 登录后台：http://your-domain.com/wp-admin
```

### 3.6 HTTPS 配置（SSL 证书）

```bash
# Let's Encrypt 免费证书
sudo apt install -y certbot python3-certbot-nginx     # Nginx
# sudo apt install -y certbot python3-certbot-apache  # Apache

sudo certbot --nginx -d example.com -d www.example.com

# 自动续期测试
sudo certbot renew --dry-run

# 更新 WordPress 地址
# 后台 → 设置 → 常规 → WordPress 地址(URL) → https://example.com
# 站点地址(URL) → https://example.com
```

---

## 四、后台管理

### 4.1 后台界面概览

```
/wp-admin                    后台登录页面
/dashboard                   仪表盘（主页）
/posts                       文章管理
/media                       媒体库
/pages                       页面管理
/comments                    评论管理
/appearance                  外观（主题、菜单、小工具、自定义）
/plugins                     插件管理
/users                       用户管理
/tools                       工具（导入/导出、站点健康）
/settings                    设置
```

### 4.2 仪表盘

```
概览小工具：
├── 站点健康状态           检查服务器和站点问题
├── 快速草稿               快速创建文章
├── 近期评论               管理评论
├── 活动                   最近文章/计划文章
├── WordPress 新闻         官方动态
└── 概览                   页面/文章/评论统计
```

### 4.3 用户管理

```
用户角色（权限从低到高）：
├── 订阅者（Subscriber）    仅能阅读
├── 贡献者（Contributor）   撰写文章（不能发布）
├── 作者（Author）          发布和管理自己的文章
├── 编辑（Editor）          管理所有文章和评论
└── 管理员（Administrator） 完全控制

操作：
├── 添加用户
├── 编辑用户资料
├── 修改密码
├── 删除用户（可将内容转移给其他用户）
└── 修改用户角色
```

### 4.4 常规设置

```
设置 → 常规：
├── 站点标题
├── 副标题（Tagline）
├── WordPress 地址 (URL)
├── 站点地址 (URL)
├── 电子邮件地址
├── 成员资格（是否允许注册）
├── 新用户默认角色
├── 站点语言
├── 时区、日期格式、时间格式
└── 一周开始于

设置 → 撰写：
├── 默认文章分类
├── 默认文章标签
├── 默认编辑器（经典/区块）
└── 发布方式

设置 → 阅读：
├── 主页显示（最新文章 / 静态页面）
├── 博客页面显示文章数
├── 摘要 / 全文
├── Feed 中显示文章数
├── 搜索引擎可见性（开发时勾选）

设置 → 讨论（评论）：
├── 默认文章设置
├── 评论审核
├── 评论黑名单
├── 头像

设置 → 媒体：
├── 缩略图尺寸（小/中/大）
└── 上传文件按年月组织

设置 → 固定链接（重要）：
├── 朴素：?p=123
├── 日期：/2024/01/15/post-name/
├── 月份：/2024/01/post-name/
├── 数字：/archives/123
├── 文章名：/post-name/          ← 推荐（SEO 友好）
└── 自定义结构：/%category%/%postname%/
```

---

## 五、内容管理

### 5.1 文章管理

```
文章 → 所有文章：
├── 搜索 / 筛选（分类、标签、日期、作者）
├── 批量操作（编辑、移动到回收站）
├── 快速编辑（标题、别名、日期、状态、分类）
└── 查看（在新标签打开）

文章分类：
├── 分类名称
├── 别名（URL slug）
├── 父分类（层级分类）
├── 描述（部分主题显示）
└── 分类链接：/category/category-name/

文章标签：
├── 标签名称
├── 别名
└── 标签链接：/tag/tag-name/

文章格式（部分主题支持）：
├── 标准（Standard）
├── 图像（Image）
├── 画廊（Gallery）
├── 视频（Video）
├── 音频（Audio）
├── 引用（Quote）
├── 链接（Link）
└── 状态（Status）
```

### 5.2 Gutenberg 区块编辑器

```
区块编辑器（WordPress 5.0+ 默认）：

常用区块：
├── 段落（Paragraph）
├── 标题（Heading）H1-H6
├── 图像（Image）
├── 列表（List）
├── 引用（Quote）
├── 代码（Code）
├── 表格（Table）
├── 分隔线（Separator）
├── 按钮（Button）
├── 列（Columns）多列布局
├── 组（Group）区块组合
├── 视频 / 音频
├── 短代码（Shortcode）
├── HTML（自定义 HTML）
├── 最新文章（Latest Posts）
├── 最新评论（Latest Comments）
└── 社交图标（Social Icons）

编辑器快捷键：
├── Ctrl + Shift + D          复制区块
├── Ctrl + Shift + Z          撤销
├── /                         快速插入区块
├── Ctrl + K                  插入链接
├── Ctrl + B                  加粗
├── Ctrl + I                  斜体
└── Ctrl + Shift + Alt + M    切换 HTML 模式

区块模式（Patterns）：
└── 预定义的区块组合模板
```

### 5.3 页面管理

```
页面（Pages）vs 文章（Posts）：

页面：
├── 静态内容（关于我们、联系方式、隐私政策）
├── 无分类 / 无标签
├── 可设层级（父子页面）
├── 可设首页 / 博客页
└── 通常不在 RSS 中

文章：
├── 动态内容（博客文章、新闻）
├── 有分类和标签
├── 有发布时间
├── 出现在 RSS 中
└── 支持评论

常用页面：
├── 首页（Home）
├── 关于我们（About）
├── 联系方式（Contact）
├── 隐私政策（Privacy Policy）
├── 服务条款（Terms of Service）
├── 404 页面
└── 搜索结果页
```

### 5.4 媒体管理

```
媒体库：
├── 上传文件（图片、视频、音频、文档）
├── 最大上传大小（php.ini 中 upload_max_filesize）
├── 支持格式：jpg/png/gif/webp/svg/pdf/zip 等

媒体设置：
├── 缩略图大小（小/中/大）
├── 上传路径
└── 按年月组织文件

图片优化：
├── 上传前压缩（TinyPNG / Squoosh）
├── 使用 WebP 格式
├── 插件：ShortPixel / Imagify / Smush
└── 懒加载（Lazy Load）
```

### 5.5 评论管理

```
评论设置：
├── 评论审核（需手动审核 / 自动通过）
├── 评论者需填写姓名和邮箱
├── 已登录用户自动通过
├── 评论包含链接时需审核
├── 评论黑名单（关键词、IP、邮箱）
├── 关闭旧文章评论
├── 评论嵌套深度
└── 评论分页

反垃圾评论：
├── Akismet（官方反垃圾插件）
├── reCAPTCHA 插件
├── 验证码插件
└── 手动审核
```

---

## 六、主题使用与配置

### 6.1 安装主题

```
方式一：后台安装
外观 → 主题 → 添加新主题 → 搜索/上传

方式二：上传 ZIP
外观 → 主题 → 添加新主题 → 上传主题 → 选择 ZIP 文件

方式三：FTP 上传
将主题文件夹上传到 /wp-content/themes/

方式四：WP-CLI
wp theme install theme-name --activate
```

### 6.2 主题推荐

```
免费主题：
├── Astra            轻量、速度快、高度可定制
├── GeneratePress    轻量、性能优秀
├── Kadence          功能丰富、性能好
├── Hello Elementor  Elementor 专用
├── Flavor flavor    博客主题
├── Flavor flavor    Flavor flavor   Flavor flavor
└── Flavor flavor    Flavor flavor

付费主题：
├── Flavor flavor    flavor flavor
├── Flavor flavor    Flavor flavor
├── Flavor flavor    Flavor flavor
└── Flavor flavor    Flavor flavor

页面构建器：
├── Elementor        最流行的页面构建器
├── Divi             Elegant Themes 出品
├── Beaver Builder   稳定可靠
├── WPBakery         经典构建器
└── Gutenberg 原生   WordPress 自带
```

### 6.3 自定义主题

```
外观 → 自定义（Customizer）：
├── 站点身份（Logo、站点标题、图标）
├── 颜色（主色调、背景色）
├── 排版（字体、字号、行高）
├── 布局（容器宽度、侧边栏位置）
├── 页眉（Header）设置
├── 页脚（Footer）设置
├── 菜单设置
├── 小工具（Widgets）
├── 首页设置
├── 额外 CSS
└── 各主题特有设置

外观 → 菜单：
├── 创建菜单
├── 添加页面/文章/自定义链接/分类
├── 拖拽排序
├── 设置菜单位置（主菜单、页脚菜单等）
└── 多级菜单（子菜单）

外观 → 小工具：
├── 文章列表
├── 分类列表
├── 标签云
├── 搜索框
├── 日历
├── 自定义 HTML
├── 最新评论
├── 最新文章
└── 社交图标
```

### 6.4 子主题（Child Theme）

```bash
# 子主题允许在不修改父主题的情况下自定义
# 创建子主题目录
mkdir /var/www/wordpress/wp-content/themes/parent-child
```

**style.css（必须文件）：**
```css
/*
 Theme Name:   Parent Child
 Theme URI:    https://example.com
 Description:  Child theme for Parent Theme
 Author:       Your Name
 Template:     parent-theme-folder-name    ← 父主题文件夹名
 Version:      1.0.0
*/

/* 子主题自定义样式 */
```

**functions.php：**
```php
<?php
// 加载父主题样式
function child_theme_styles() {
    wp_enqueue_style('parent-style', get_template_directory_uri() . '/style.css');
    wp_enqueue_style('child-style', get_stylesheet_uri(), array('parent-style'), wp_get_theme()->get('Version'));
}
add_action('wp_enqueue_scripts', 'child_theme_styles');
```

```bash
# 启用子主题
# 后台 → 外观 → 主题 → 激活 "Parent Child"
```

---

## 七、插件管理

### 7.1 安装插件

```
方式一：后台安装
插件 → 安装插件 → 搜索/上传/推荐

方式二：上传 ZIP
插件 → 安装插件 → 上传插件 → 选择 ZIP

方式三：FTP
上传到 /wp-content/plugins/

方式四：WP-CLI
wp plugin install plugin-name --activate
```

### 7.2 必装插件推荐

```
SEO 优化：
├── Yoast SEO            最流行的 SEO 插件
├── Rank Math            功能更丰富的 SEO 插件
└── All in One SEO       经典 SEO 插件

性能优化：
├── WP Rocket            付费缓存插件（推荐）
├── LiteSpeed Cache      免费（LiteSpeed 服务器）
├── W3 Total Cache       免费缓存插件
├── WP Super Cache       免费缓存插件
├── Autoptimize          CSS/JS 优化
└── ShortPixel / Imagify 图片优化

安全：
├── Wordfence            防火墙 + 恶意扫描
├── Sucuri Security      安全监控
├── iThemes Security     安全加固
├── Limit Login Attempts 限制登录尝试
└── UpdraftPlus          备份插件

备份：
├── UpdraftPlus          备份/恢复
├── BackWPup             备份到云端
└── Duplicator           站点迁移/备份

表单：
├── Contact Form 7       免费联系表单
├── WPForms              可视化表单构建
├── Gravity Forms        付费高级表单
└── Ninja Forms          免费表单

缓存/CDN：
├── WP Rocket            付费缓存（推荐）
├── Cloudflare           CDN + 安全
└── Autoptimize          资源优化

电商：
├── WooCommerce          电商插件
└── Easy Digital Downloads  数字商品

其他：
├── Akismet              反垃圾评论（官方）
├── Redirection          301 重定向管理
├── UpdraftPlus          备份
├── WP-Optimize          数据库优化
├── Smush                图片压缩
├── Really Simple SSL    SSL 配置
└── MonsterInsights      Google Analytics 统计
```

### 7.3 插件冲突排查

```bash
# 1. 禁用所有插件
# 后台 → 插件 → 批量操作 → 停用

# 2. 或通过 WP-CLI
wp plugin deactivate --all

# 3. 或通过数据库
mysql -u root -p wordpress -e "UPDATE wp_options SET option_value='a:0:{}' WHERE option_name='active_plugins';"

# 4. 逐个启用，找到冲突插件

# 5. 切换默认主题测试
wp theme activate twentytwentyfour

# 6. 开启调试模式
# wp-config.php 中添加：
# define('WP_DEBUG', true);
# define('WP_DEBUG_LOG', true);
# define('WP_DEBUG_DISPLAY', false);
# 查看 /wp-content/debug.log
```

---

## 八、主题开发

### 8.1 主题文件结构

```
my-theme/
├── style.css                  主题样式（必须，含主题信息）
├── functions.php              主题功能（必须）
├── index.php                  默认模板（必须）
├── header.php                 页眉模板
├── footer.php                 页脚模板
├── sidebar.php                侧边栏模板
├── single.php                 单篇文章模板
├── page.php                   页面模板
├── archive.php                归档模板
├── search.php                 搜索结果模板
├── 404.php                    404 页面模板
├── comments.php               评论模板
├── screenshot.png             主题截图（1200x900）
├── assets/
│   ├── css/
│   │   └── main.css
│   ├── js/
│   │   └── main.js
│   └── images/
├── template-parts/
│   ├── content.php
│   ├── content-none.php
│   └── content-page.php
├── inc/
│   ├── customizer.php         自定义设置
│   ├── widgets.php            小工具
│   └── template-tags.php      模板标签
└── languages/
    ├── my-theme.pot           翻译模板
    └── zh_CN.po
```

### 8.2 核心模板文件

**style.css（主题信息）：**
```css
/*
 Theme Name:   My Custom Theme
 Theme URI:    https://example.com
 Author:       Your Name
 Author URI:   https://example.com
 Description:  A custom WordPress theme
 Version:      1.0.0
 License:      GNU General Public License v2 or later
 License URI:  https://www.gnu.org/licenses/gpl-2.0.html
 Text Domain:  my-theme
 Tags:         blog, custom-menu, custom-logo, featured-images
*/
```

**functions.php（核心功能）：**
```php
<?php
/**
 * My Theme Functions
 */

// 主题支持
function my_theme_setup() {
    // 支持标题标签
    add_theme_support('title-tag');

    // 支持特色图片
    add_theme_support('post-thumbnails');

    // 支持自定义 Logo
    add_theme_support('custom-logo', array(
        'height'      => 100,
        'width'       => 300,
        'flex-height' => true,
        'flex-width'  => true,
    ));

    // 支持 HTML5 标签
    add_theme_support('html5', array(
        'search-form', 'comment-form', 'comment-list',
        'gallery', 'caption', 'style', 'script',
    ));

    // 注册导航菜单
    register_nav_menus(array(
        'primary'   => __('Primary Menu', 'my-theme'),
        'footer'    => __('Footer Menu', 'my-theme'),
        'sidebar'   => __('Sidebar Menu', 'my-theme'),
    ));

    // 自定义图片尺寸
    add_image_size('featured-large', 1200, 630, true);
    add_image_size('card-thumb', 400, 300, true);
}
add_action('after_setup_theme', 'my_theme_setup');

// 注册侧边栏
function my_theme_widgets_init() {
    register_sidebar(array(
        'name'          => __('Main Sidebar', 'my-theme'),
        'id'            => 'main-sidebar',
        'description'   => __('Add widgets here', 'my-theme'),
        'before_widget' => '<section id="%1$s" class="widget %2$s">',
        'after_widget'  => '</section>',
        'before_title'  => '<h3 class="widget-title">',
        'after_title'   => '</h3>',
    ));

    register_sidebar(array(
        'name'          => __('Footer Widgets', 'my-theme'),
        'id'            => 'footer-widgets',
        'before_widget' => '<div class="footer-widget %2$s">',
        'after_widget'  => '</div>',
        'before_title'  => '<h4 class="footer-widget-title">',
        'after_title'   => '</h4>',
    ));
}
add_action('widgets_init', 'my_theme_widgets_init');

// 加载样式和脚本
function my_theme_scripts() {
    wp_enqueue_style('my-theme-style', get_stylesheet_uri(), array(), wp_get_theme()->get('Version'));
    wp_enqueue_style('my-theme-main', get_template_directory_uri() . '/assets/css/main.css', array(), '1.0.0');
    wp_enqueue_script('my-theme-main', get_template_directory_uri() . '/assets/js/main.js', array(), '1.0.0', true);

    // 添加自定义属性到 WordPress 生成的 JS（如 AJAX nonce）
    wp_localize_script('my-theme-main', 'myThemeData', array(
        'ajaxUrl' => admin_url('admin-ajax.php'),
        'nonce'   => wp_create_nonce('my_theme_nonce'),
    ));
}
add_action('wp_enqueue_scripts', 'my_theme_scripts');

// 自定义摘要长度
function my_theme_excerpt_length($length) {
    return 30;
}
add_filter('excerpt_length', 'my_theme_excerpt_length');

// 自定义摘要后缀
function my_theme_excerpt_more($more) {
    return '...';
}
add_filter('excerpt_more', 'my_theme_excerpt_more');
```

**header.php：**
```php
<!DOCTYPE html>
<html <?php language_attributes(); ?>>
<head>
    <meta charset="<?php bloginfo('charset'); ?>">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <?php wp_head(); ?>
</head>
<body <?php body_class(); ?>>
<?php wp_body_open(); ?>

<header class="site-header">
    <div class="container">
        <div class="site-branding">
            <?php
            if (has_custom_logo()) {
                the_custom_logo();
            } else {
                echo '<h1 class="site-title"><a href="' . esc_url(home_url('/')) . '">' . get_bloginfo('name') . '</a></h1>';
            }
            ?>
        </div>

        <nav class="main-navigation">
            <?php
            wp_nav_menu(array(
                'theme_location' => 'primary',
                'container'      => false,
                'menu_class'     => 'nav-menu',
            ));
            ?>
        </nav>
    </div>
</header>

<main class="site-main">
```

**footer.php：**
```php
</main>

<footer class="site-footer">
    <div class="container">
        <?php
        if (is_active_sidebar('footer-widgets')) {
            dynamic_sidebar('footer-widgets');
        }
        ?>

        <nav class="footer-navigation">
            <?php
            wp_nav_menu(array(
                'theme_location' => 'footer',
                'container'      => false,
                'menu_class'     => 'footer-menu',
            ));
            ?>
        </nav>

        <p class="copyright">
            &copy; <?php echo date('Y'); ?> <?php bloginfo('name'); ?>.
            <?php _e('All rights reserved.', 'my-theme'); ?>
        </p>
    </div>
</footer>

<?php wp_footer(); ?>
</body>
</html>
```

**index.php：**
```php
<?php get_header(); ?>

<div class="container">
    <?php if (have_posts()) : ?>
        <div class="posts-grid">
            <?php while (have_posts()) : the_post(); ?>
                <article id="post-<?php the_ID(); ?>" <?php post_class('post-card'); ?>>
                    <?php if (has_post_thumbnail()) : ?>
                        <a href="<?php the_permalink(); ?>" class="post-thumbnail">
                            <?php the_post_thumbnail('card-thumb'); ?>
                        </a>
                    <?php endif; ?>

                    <div class="post-content">
                        <h2 class="post-title">
                            <a href="<?php the_permalink(); ?>"><?php the_title(); ?></a>
                        </h2>

                        <div class="post-meta">
                            <span class="post-date"><?php echo get_the_date(); ?></span>
                            <span class="post-author"><?php the_author(); ?></span>
                            <span class="post-category"><?php the_category(', '); ?></span>
                        </div>

                        <div class="post-excerpt">
                            <?php the_excerpt(); ?>
                        </div>

                        <a href="<?php the_permalink(); ?>" class="read-more">
                            <?php _e('Read More', 'my-theme'); ?> →
                        </a>
                    </div>
                </article>
            <?php endwhile; ?>
        </div>

        <?php the_posts_pagination(array(
            'mid_size'  => 2,
            'prev_text' => __('← Previous', 'my-theme'),
            'next_text' => __('Next →', 'my-theme'),
        )); ?>

    <?php else : ?>
        <p><?php _e('No posts found.', 'my-theme'); ?></p>
    <?php endif; ?>
</div>

<?php get_footer(); ?>
```

**single.php：**
```php
<?php get_header(); ?>

<div class="container">
    <?php while (have_posts()) : the_post(); ?>
        <article id="post-<?php the_ID(); ?>" <?php post_class('single-post'); ?>>
            <header class="entry-header">
                <h1 class="entry-title"><?php the_title(); ?></h1>
                <div class="post-meta">
                    <span><?php echo get_the_date(); ?></span>
                    <span><?php the_author(); ?></span>
                    <?php the_category(', '); ?>
                </div>
            </header>

            <?php if (has_post_thumbnail()) : ?>
                <div class="post-thumbnail">
                    <?php the_post_thumbnail('featured-large'); ?>
                </div>
            <?php endif; ?>

            <div class="entry-content">
                <?php the_content(); ?>
                <?php wp_link_pages(); ?>
            </div>

            <footer class="entry-footer">
                <div class="tags"><?php the_tags('', ', ', ''); ?></div>
            </footer>

            <?php
            // 上一篇/下一篇
            the_post_navigation(array(
                'prev_text' => '<span class="nav-subtitle">' . __('Previous:', 'my-theme') . '</span> <span class="nav-title">%title</span>',
                'next_text' => '<span class="nav-subtitle">' . __('Next:', 'my-theme') . '</span> <span class="nav-title">%title</span>',
            ));
            ?>

            <?php comments_template(); ?>
        </article>
    <?php endwhile; ?>
</div>

<?php get_footer(); ?>
```

**404.php：**
```php
<?php get_header(); ?>

<div class="container error-404">
    <h1>404</h1>
    <h2><?php _e('Page Not Found', 'my-theme'); ?></h2>
    <p><?php _e('The page you are looking for does not exist.', 'my-theme'); ?></p>

    <?php get_search_form(); ?>

    <a href="<?php echo esc_url(home_url('/')); ?>" class="btn">
        <?php _e('Back to Home', 'my-theme'); ?>
    </a>
</div>

<?php get_footer(); ?>
```

### 8.3 模板层次结构

```
WordPress 模板加载顺序（示例：单篇文章）：

single-{post_type}-{slug}.php     如 single-post-hello.php
single-{post_type}.php            如 single-post.php
single.php                        通用单篇模板
singular.php                      通用单数模板
index.php                         最终回退模板

页面模板顺序：
page-{slug}.php                   如 page-about.php
page-{id}.php                     如 page-2.php
page.php                          通用页面模板
singular.php
index.php

分类模板顺序：
category-{slug}.php               如 category-news.php
category-{id}.php                 如 category-3.php
category.php
archive.php
index.php

搜索模板：
search.php
index.php

404 模板：
404.php
index.php
```

### 8.4 常用模板标签

```php
// 文章
the_ID();                         // 文章 ID
the_title();                      // 标题
the_content();                    // 正文
the_excerpt();                    // 摘要
the_permalink();                  // 文章链接
the_author();                     // 作者
the_date();                       // 发布日期
the_category(', ');               // 分类
the_tags('', ', ', '');           // 标签
the_post_thumbnail('medium');     // 特色图片
the_comments_number();            // 评论数
edit_post_link('Edit');           // 编辑链接

// 查询
have_posts();                     // 是否有文章
the_post();                       // 移到下一篇文章

// 条件标签
is_home()                         // 是否首页
is_front_page()                   // 是否静态首页
is_single()                       // 是否单篇文章
is_page()                         // 是否页面
is_archive()                      // 是否归档
is_category()                     // 是否分类页
is_tag()                          // 是否标签页
is_search()                       // 是否搜索页
is_404()                          // 是否 404
is_user_logged_in()               // 是否已登录
is_admin()                        // 是否后台
is_singular('post')               // 是否单篇特定类型

// 站点信息
bloginfo('name');                 // 站点名称
bloginfo('description');          // 站点描述
bloginfo('url');                  // 站点 URL
home_url('/');                    // 首页 URL
site_url('/');                    // 站点 URL
admin_url('/');                   // 后台 URL
get_template_directory_uri();     // 主题 URL
get_stylesheet_directory_uri();   // 子主题 URL
get_template_directory();         // 主题路径

// 自定义字段
get_post_meta($post_id, 'key', true);
the_meta();                       // 输出所有自定义字段

// 菜单
wp_nav_menu(array('theme_location' => 'primary'));

// 小工具
dynamic_sidebar('sidebar-id');
is_active_sidebar('sidebar-id');
```

---

### 继续请看第二部分！
> 全面涵盖安装、系统配置、命令行、软件包管理、网络配置、服务管理、存储管理、Shell 脚本、开发环境搭建、服务器运维、安全加固、性能优化、备份恢复及故障排查。

---

## 目录

- [一、Debian 简介](#一debian-简介)
- [二、系统安装](#二系统安装)
- [三、初始配置](#三初始配置)
- [四、命令行基础](#四命令行基础)
- [五、文件与目录管理](#五文件与目录管理)
- [六、用户与权限管理](#六用户与权限管理)
- [七、软件包管理](#七软件包管理)
- [八、网络配置与管理](#八网络配置与管理)
- [九、服务与进程管理](#九服务与进程管理)
- [十、磁盘与存储管理](#十磁盘与存储管理)
- [十一、Shell 脚本编程](#十一shell-脚本编程)
- [十二、开发环境搭建](#十二开发环境搭建)
- [十三、服务器运维](#十三服务器运维)
- [十四、安全加固](#十四安全加固)
- [十五、性能优化与监控](#十五性能优化与监控)
- [十六、备份与恢复](#十六备份与恢复)
- [十七、故障排查](#十七故障排查)
- [十八、版本管理与升级](#十八版本管理与升级)
- [十九、完整实战示例](#十九完整实战示例)

---

## 一、Debian 简介

### 什么是 Debian

Debian 是历史最悠久、最稳定的 GNU/Linux 发行版之一，始于 1993 年，由全球志愿者社区开发维护。它是众多发行版（包括 Ubuntu）的上游基础。

### 核心理念

| 理念 | 说明 |
|------|------|
| **自由软件** | 遵守 DFSG（Debian 自由软件指南） |
| **稳定性优先** | 软件包经过严格测试后才进入 stable |
| **社区驱动** | 无商业公司控制，由开发者社区决策 |
| **Universal OS** | 支持多种架构和用途 |

### 发行版分支

| 分支 | 代号（当前） | 更新频率 | 适用场景 |
|------|-------------|----------|----------|
| **Stable（稳定版）** | Bookworm (12) | 每 2 年 | 生产服务器、日常桌面 |
| **Testing（测试版）** | Trixie (13) | 滚动更新 | 开发、尝鲜 |
| **Unstable（不稳定版）** | Sid | 滚动更新 | 开发者测试 |
| **Oldstable（旧稳定版）** | Bullseye (11) | 安全更新 | 过渡期使用 |
| **Backports** | 随 stable | 按需 | 获取新版软件 |

### 与 Ubuntu 的对比

| 特性 | Debian | Ubuntu |
|------|--------|--------|
| 开发方 | 社区志愿者 | Canonical 公司 |
| 发布周期 | 约 2 年 | 6 个月 / 2 年 LTS |
| 软件新旧 | 偏旧（稳定优先） | 较新 |
| 桌面体验 | 需手动配置 | 开箱即用 |
| 商业支持 | 无官方 | Canonical 提供 |
| 默认非自由固件 | 默认不含 | 默认包含 |
| Snap/Flatpak | 不默认安装 | Snap 默认 |
| PPA | 不支持 | 支持 |
| 服务器占有率 | 极高 | 很高 |
| 适合人群 | 进阶用户、运维 | 新手、开发者 |

### 适用场景

- 生产服务器（Web、数据库、邮件、DNS 等）
- 嵌入式与物联网设备
- 需要极致稳定性的环境
- 桌面工作站（进阶用户）
- 容器基础镜像（Docker 常用 base image）
- 学习 Linux 底层原理

### 官方资源

- 官网：https://www.debian.org
- 文档：https://www.debian.org/doc
- 手册：https://www.debian.org/doc/manuals/debian-handbook
- Wiki：https://wiki.debian.org
- 软件包搜索：https://packages.debian.org
- 社区论坛：https://forums.debian.net
- Bug 追踪：https://bugs.debian.org

---

## 二、系统安装

### 2.1 下载镜像

从官网下载：https://www.debian.org/download

**镜像类型：**

| 镜像 | 大小 | 说明 |
|------|------|------|
| netinst（网络安装） | ~600MB | 最小镜像，安装时联网下载（推荐） |
| DVD-1 | ~3.7GB | 包含常用软件 |
| DVD-1/2/3 套装 | ~11GB | 完整软件包 |
| CD 镜像（各架构） | ~650MB | 老式 CD |
| 云镜像 | ~400MB | AWS / Azure / GCP |

**验证镜像完整性：**
```bash
# 校验 SHA256
sha256sum -c SHA256SUMS 2>&1 | grep -v 'FAILED' | grep OK

# 校验 GPG 签名
gpg --verify SHA256SUMS.sign SHA256SUMS

# 下载签名密钥
gpg --keyserver keyring.debian.org --recv-keys 647F28654894E3BD457199BE38DBBDC86092693E
```

### 2.2 制作启动盘

**Linux 下：**
```bash
lsblk                                     # 确认 U 盘设备
sudo dd if=debian-12.x-amd64-netinst.iso of=/dev/sdX bs=4M status=progress
sync
```

**Windows 下（Rufus）：**
```
1. 打开 Rufus
2. 选择 U 盘和 Debian ISO
3. 分区类型：GPT（UEFI）/ MBR（Legacy BIOS）
4. 开始写入
```

**跨平台（balenaEtcher）：**
```
1. Flash from file → 选择 ISO
2. Select target → 选择 U 盘
3. Flash!
```

### 2.3 虚拟机安装（推荐入门）

**VMware Workstation：**
```
1. 新建虚拟机 → 典型
2. 选择"稍后安装操作系统"
3. 客户机操作系统：Linux → Debian 12.x 64-bit
4. 磁盘 ≥ 40GB（桌面）/ ≥ 20GB（服务器）
5. 内存 ≥ 4GB（推荐 8GB），CPU ≥ 2 核
6. 网络：NAT / 桥接 / 仅主机
7. 选择 ISO 镜像 → 开启
```

**VirtualBox：**
```
1. 新建 → 类型：Linux，版本：Debian (64-bit)
2. 内存 ≥ 4096MB
3. 创建 VDI 动态磁盘，≥ 40GB
4. 设置 → 存储 → 选择 ISO
5. 启动
```

### 2.4 安装步骤详解

```
1. 启动菜单：
   - Graphical install（图形安装，推荐）
   - Install（文本安装）
   - Advanced options
   - Accessibility options

2. 语言选择：English（推荐安装用英文，后续可装中文）
   地区：China / United States
   键盘：American English

3. 网络配置：
   主机名：debian（自定义）
   域名：留空或填写

4. 用户与密码：
   Root 密码：设置（牢记，Debian 默认启用 root）
   普通用户名：自定义
   普通用户密码：设置

5. 磁盘分区：
   - Guided - use entire disk（整个磁盘，推荐）
   - Guided - use entire disk and set up LVM
   - Guided - use entire disk and set up encrypted LVM
   - Manual（手动分区）

   分区方案：
   │ 所有文件放在一个分区（推荐新手）
   │ /home 独立分区（推荐服务器）
   │ /home、/var、/tmp 各自独立（高级）

6. 软件选择（tasksel）：
   - Debian desktop environment（桌面）
   - ... GNOME / Xfce / KDE / LXDE / LXQt / MATE
   - web server
   - SSH server
   - print server
   - 标准系统工具（推荐勾选）
   - 什么都不选（最小安装）

7. 安装 GRUB 引导：
   - Yes（安装到主驱动器）
   - 选择安装设备（如 /dev/sda）

8. 安装完成 → 重启
```

**推荐分区方案（服务器 / 手动分区）：**
```
/boot       1GB       ext4    引导分区（可选独立）
/           20-30GB   ext4    根分区
swap        内存 1-2 倍  swap  交换分区（内存 ≥ 16GB 可省略）
/var        10-20GB   ext4    日志与数据（服务器推荐）
/home       剩余空间   ext4    用户数据
```

### 2.5 固件与非自由软件

```bash
# Debian 默认不包含非自由固件（如 WiFi 驱动）
# 安装时如果缺少固件，可以：

# 方法 1：使用包含非自由固件的安装镜像
# 下载 "non-free firmware" 版本的 ISO

# 方法 2：安装后手动添加 non-free 源
sudo nano /etc/apt/sources.list
# 添加 non-free non-free-firmware 组件
# deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware

sudo apt update
sudo apt install firmware-linux-nonfree   # 通用固件
sudo apt install firmware-iwlwifi         # Intel WiFi 固件
sudo apt install firmware-realtek         # Realtek 固件
```

### 2.6 最小安装与服务器安装

```bash
# 最小安装（无桌面）
# 安装时只勾选 "SSH server" 和 "standard system tools"

# 安装后按需添加
sudo apt install -y task-gnome-desktop    # GNOME 桌面
sudo apt install -y task-xfce-desktop     # Xfce 桌面
sudo apt install -y task-kde-desktop      # KDE 桌面

# 服务器常用组件
sudo apt install -y nginx mysql-server redis-server
```

### 2.7 双系统安装（Windows + Debian）

```
1. Windows 中压缩磁盘，留出 ≥ 40GB 未分配空间
2. BIOS 设置：关闭 Secure Boot、Fast Boot
3. 安装时选择 Manual 分区
4. 在未分配空间上创建分区（不动 Windows 分区）
5. GRUB 自动检测 Windows
6. 重启后选择系统
```

---

## 三、初始配置

### 3.1 配置 apt 源

```bash
# 查看当前源
cat /etc/apt/sources.list

# 备份
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

# Debian 12 (bookworm) 配置示例
sudo nano /etc/apt/sources.list
```

**官方源：**
```
deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
```

**国内镜像源（阿里云）：**
```
deb https://mirrors.aliyun.com/debian bookworm main contrib non-free non-free-firmware
deb https://mirrors.aliyun.com/debian bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.aliyun.com/debian-security bookworm-security main contrib non-free non-free-firmware
```

**国内镜像源（清华）：**
```
deb https://mirrors.tuna.tsinghua.edu.cn/debian bookworm main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware
```

**国内镜像源（中科大）：**
```
deb https://mirrors.ustc.edu.cn/debian bookworm main contrib non-free non-free-firmware
deb https://mirrors.ustc.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.ustc.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware
```

**组件说明：**
| 组件 | 说明 |
|------|------|
| `main` | DFSG 兼容的自由软件 |
| `contrib` | 依赖非自由软件的自由软件 |
| `non-free` | 不符合 DFSG 的软件 |
| `non-free-firmware` | 非自由固件（Debian 12+） |

```bash
# 更新
sudo apt update
```

**启用 Backports（获取新版软件）：**
```bash
# 添加 backports 源
echo "deb https://mirrors.aliyun.com/debian bookworm-backports main contrib non-free non-free-firmware" | sudo tee /etc/apt/sources.list.d/backports.list

sudo apt update

# 从 backports 安装
sudo apt install -t bookworm-backports package_name
```

### 3.2 系统更新

```bash
sudo apt update                              # 更新索引
sudo apt upgrade                             # 升级已安装包
sudo apt full-upgrade                        # 完整升级（处理依赖变化）
sudo apt autoremove -y                       # 清理不需要的依赖
sudo apt autoclean                           # 清理旧包缓存

# 查看可升级的包
apt list --upgradable

# 查看安全更新
sudo apt list --upgradable | grep security
```

### 3.3 创建 sudo 用户

```bash
# Debian 默认安装后使用 root，建议创建 sudo 用户

# 创建用户
sudo adduser username

# 安装 sudo（最小安装可能未包含）
su -
apt install -y sudo

# 将用户加入 sudo 组
usermod -aG sudo username

# 验证
su - username
sudo whoami          # 输出 root

# 配置 sudo 免密码（可选）
sudo visudo
# username ALL=(ALL:ALL) NOPASSWD: ALL
```

### 3.4 设置主机名

```bash
# 查看
hostname
hostnamectl

# 修改（永久）
sudo hostnamectl set-hostname debian-server

# 修改 /etc/hosts
sudo nano /etc/hosts
# 127.0.1.1    debian-server
```

### 3.5 时间与时区

```bash
# 设置时区
sudo timedatectl set-timezone Asia/Shanghai

# 开启 NTP 同步
sudo timedatectl set-ntp true

# 查看
timedatectl
date

# 手动设置（如 NTP 不可用）
sudo date -s "2024-01-15 10:30:00"
```

### 3.6 中文支持

```bash
# 安装中文字体
sudo apt install -y fonts-wqy-zenhei fonts-wqy-microhei fonts-noto-cjk

# 安装中文输入法（Fcitx5）
sudo apt install -y fcitx5 fcitx5-chinese-addons

# 设置环境变量
cat >> ~/.profile << 'EOF'
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
EOF

# 设置系统语言
sudo dpkg-reconfigure locales
# 选择 zh_CN.UTF-8 UTF-8
```

### 3.7 SSH 配置

```bash
# 安装 SSH 服务
sudo apt install -y openssh-server

# 启动
sudo systemctl enable ssh
sudo systemctl start ssh

# 远程连接
ssh username@192.168.1.100

# 密钥登录
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh-copy-id username@192.168.1.100

# SSH 客户端配置（~/.ssh/config）
Host myserver
    HostName 192.168.1.100
    User username
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

**SSH 服务端安全配置：**
```bash
sudo nano /etc/ssh/sshd_config

Port 2222                       # 修改端口
PermitRootLogin no              # 禁止 root 登录
PasswordAuthentication no       # 仅密钥登录
MaxAuthTries 3                  # 最大尝试次数
ClientAliveInterval 300         # 空闲超时

sudo systemctl restart ssh
```

### 3.8 基础软件安装

```bash
sudo apt install -y \
    curl wget git vim nano htop tree unzip zip \
    net-tools openssh-server \
    build-essential \
    software-properties-common \
    apt-transport-https ca-certificates gnupg lsb-release \
    sudo bash-completion

# 桌面环境（可选）
sudo apt install -y task-xfce-desktop    # Xfce（轻量）
sudo apt install -y task-gnome-desktop   # GNOME
sudo apt install -y task-kde-desktop     # KDE

# 常用桌面工具
sudo apt install -y firefox-esr vlc gparted flameshot terminator
```

---

## 四、命令行基础

### 4.1 终端使用

```bash
# 快捷键
Ctrl + Alt + T        # 打开终端（桌面环境）
Ctrl + C              # 终止命令
Ctrl + Z              # 暂停任务（fg / bg）
Ctrl + D              # 退出 / EOF
Ctrl + R              # 搜索历史
Ctrl + L              # 清屏
Ctrl + A / E          # 行首 / 行尾
Ctrl + U / K          # 删除行前 / 行后
Ctrl + W              # 删除前一个单词
Tab                   # 自动补全
!!                    # 重复上一条命令
sudo !!               # sudo 执行上一条命令
```

### 4.2 命令帮助

```bash
man command           # 查看手册
command --help        # 简要帮助
info command          # info 文档
whatis command        # 简要说明
which command         # 命令位置
whereis command       # 相关文件
type command          # 命令类型
apropos keyword       # 搜索命令
```

### 4.3 Shell 环境

```bash
echo $SHELL           # 当前 Shell
cat /etc/shells       # 可用 Shell

# 安装 Zsh + Oh My Zsh
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh)
```

### 4.4 环境变量

```bash
echo $PATH            # PATH 变量
env                   # 所有环境变量
printenv HOME         # 查看指定变量
export MY_VAR="val"   # 设置环境变量（临时）

# 永久设置
echo 'export MY_VAR="val"' >> ~/.bashrc
source ~/.bashrc

# 系统级设置
sudo nano /etc/environment
# PATH="/usr/local/bin:/usr/bin:/bin"
```

### 4.5 Man 手册与文档

```bash
man 5 passwd          # 查看 passwd 文件格式（第 5 章）
man 7 signal          # 查看信号（第 7 章）

# man 章节
# 1  用户命令
# 2  系统调用
# 3  C 库函数
# 4  设备文件
# 5  文件格式
# 6  游戏
# 7  杂项
# 8  系统管理命令

# Debian 手册
sudo apt install -y debian-handbook
```

---

## 五、文件与目录管理

### 5.1 文件系统结构

```
/                 根目录
├── bin           基本命令（所有用户）
├── boot          引导文件（内核、GRUB）
├── dev           设备文件
├── etc           系统配置文件
├── home          用户主目录
│   └── username
├── lib / lib64   系统库
├── media         可移动设备挂载点
├── mnt           临时挂载点
├── opt           第三方软件
├── proc          进程信息（虚拟）
├── root          root 家目录
├── run           运行时数据
├── sbin          系统管理命令
├── srv           服务数据
├── sys           系统信息（虚拟）
├── tmp           临时文件
├── usr           用户程序
│   ├── bin       用户命令
│   ├── lib       库
│   ├── local     本地安装
│   ├── sbin      系统管理
│   └── share     共享数据
└── var           可变数据
    ├── cache     缓存
    ├── log       日志
    ├── lib       状态数据
    └── www       Web 数据
```

### 5.2 目录操作

```bash
pwd                   # 当前路径
ls -lha               # 详细列出（含隐藏文件）
ls -lt                # 按时间排序
ls -lS                # 按大小排序
ls -R                 # 递归列出

cd /path              # 切换目录
cd ~ / cd             # 家目录
cd -                  # 上一个目录
cd ..                 # 上级目录

mkdir dirname         # 创建目录
mkdir -p a/b/c        # 递归创建
rmdir dirname         # 删除空目录
rm -rf dirname        # 强制递归删除（慎用）
```

### 5.3 文件操作

```bash
touch file.txt        # 创建空文件
cp file1 file2        # 复制
cp -a src/ dest/      # 递归复制（保留属性）
mv file1 file2        # 移动/重命名
rm file.txt           # 删除
rm -i file.txt        # 交互式删除
rm -f file.txt        # 强制删除
ln -s target link     # 软链接
ln target link        # 硬链接
```

### 5.4 文件查看

```bash
cat file.txt          # 全部内容
less file.txt         # 分页查看（推荐）
more file.txt         # 分页
head -n 20 file.txt   # 前 20 行
tail -n 20 file.txt   # 后 20 行
tail -f file.txt      # 实时追踪
wc -l file.txt        # 行数
file file.txt         # 文件类型
stat file.txt         # 详细信息
```

### 5.5 查找文件

```bash
# find
find / -name "*.conf"                 # 按名称
find /etc -type f -name "*.conf"      # 查找文件
find /var -type d -name "log*"        # 查找目录
find / -size +100M                    # 大于 100M
find / -mtime -7                      # 7 天内修改
find / -user www-data                 # 按用户
find / -perm 755                      # 按权限
find /tmp -name "*.tmp" -delete       # 查找并删除
find /var -name "*.log" -exec grep "error" {} \;  # 查找并执行

# locate
sudo apt install -y mlocate
sudo updatedb
locate filename

# which / whereis
which python3
whereis nginx
```

### 5.6 文本处理

```bash
# grep
grep "keyword" file.txt
grep -r "keyword" /etc/
grep -i "keyword" file.txt           # 忽略大小写
grep -n "keyword" file.txt           # 显示行号
grep -v "keyword" file.txt           # 反向
grep -c "keyword" file.txt           # 计数
grep -E "error|warn" file.txt        # 多模式
grep -A 3 "error" file.txt           # 后 3 行
grep -B 3 "error" file.txt           # 前 3 行

# sed
sed 's/old/new/g' file.txt           # 替换
sed -i 's/old/new/g' file.txt        # 原地替换
sed -n '5,10p' file.txt              # 打印 5-10 行
sed '3d' file.txt                    # 删除第 3 行

# awk
awk '{print $1}' file.txt            # 第 1 列
awk -F: '{print $1}' /etc/passwd     # 指定分隔符
awk '$3 > 100' file.txt              # 条件过滤
awk '{sum+=$1} END {print sum}'      # 求和

# sort / uniq
sort file.txt                        # 排序
sort -n file.txt                     # 数值排序
sort -r file.txt                     # 降序
sort file.txt | uniq -c              # 去重计数

# cut
cut -d: -f1 /etc/passwd
cut -c1-10 file.txt

# tr
echo "hello" | tr 'a-z' 'A-Z'       # 转大写
```

### 5.7 压缩与解压

```bash
tar -czvf a.tar.gz dir/              # 打包 gzip
tar -xzvf a.tar.gz                   # 解压 .tar.gz
tar -xjvf a.tar.bz2                  # 解压 .tar.bz2
tar -xJvf a.tar.xz                   # 解压 .tar.xz
tar -tf a.tar.gz                     # 查看内容

zip -r a.zip dir/                    # 打包 zip
unzip a.zip                          # 解压 zip

gzip file                            # gzip 压缩
gunzip file.gz                       # gzip 解压

# 7z
sudo apt install -y p7zip-full
7z x a.7z

# zstd
sudo apt install -y zstd
zstd file
zstd -d file.zst
```

---

## 六、用户与权限管理

### 6.1 用户管理

```bash
# Debian 特有：默认有 root 用户，sudo 可选

# 创建用户
sudo adduser username                # 交互式（推荐）
sudo useradd -m -s /bin/bash username

# 修改密码
sudo passwd username
passwd                               # 改自己的密码

# 删除用户
sudo deluser username                # 删除用户
sudo userdel -r username             # 删除用户及家目录

# 修改用户
sudo usermod -l newname oldname      # 改名
sudo usermod -d /new/home username   # 改家目录
sudo usermod -s /bin/zsh username    # 改 Shell
sudo usermod -aG sudo username       # 加入 sudo 组

# 查看用户
whoami
who
w
id username
groups username
last
lastlog
cat /etc/passwd
cat /etc/group
```

### 6.2 用户组管理

```bash
sudo addgroup groupname
sudo delgroup groupname
sudo usermod -aG groupname username
sudo gpasswd -d username groupname
```

### 6.3 sudo 与 su

```bash
# su：切换到 root
su -                  # 切换到 root（需要 root 密码）
su - username         # 切换到指定用户

# sudo：以 root 权限执行命令
sudo command
sudo -u username command   # 以指定用户执行

# 配置 sudo
sudo visudo

# 常用配置
# username ALL=(ALL:ALL) ALL                # 需要密码
# username ALL=(ALL:ALL) NOPASSWD: ALL      # 免密码
# %sudo ALL=(ALL:ALL) ALL                   # sudo 组所有成员

# Debian 默认 sudo 组配置
# %sudo ALL=(ALL:ALL) ALL
```

### 6.4 文件权限

```bash
# 权限：r(4) w(2) x(1)，所有者/组/其他
# 例：rwxr-xr-x = 755

chmod 755 file
chmod 644 file
chmod +x script.sh
chmod -R 755 dir/
chmod u+x file                    # 所有者添加执行
chmod g-w file                    # 组去掉写
chmod o=r file                    # 其他只读

sudo chown user:group file
sudo chown -R user:group dir/
sudo chgrp groupname file

# 特殊权限
chmod u+s file                    # SUID
chmod g+s dir                     # SGID
chmod +t dir                      # Sticky bit

# umask
umask                             # 查看
umask 022                         # 设置

# ACL
getfacl file
setfacl -m u:username:rw file
setfacl -x u:username file
```

---

## 七、软件包管理

### 7.1 APT 包管理

```bash
# 基本操作
sudo apt update                              # 更新索引
sudo apt upgrade                             # 升级
sudo apt full-upgrade                        # 完整升级
sudo apt install package_name               # 安装
sudo apt install -y package_name            # 自动确认
sudo apt remove package_name                # 卸载（保留配置）
sudo apt purge package_name                 # 彻底卸载
sudo apt autoremove                         # 清理依赖
sudo apt clean                              # 清理缓存

# 查询
apt search keyword
apt show package_name
apt list --installed
apt list --upgradable
apt list --all-versions package_name
dpkg -l | grep keyword
dpkg -L package_name                       # 查看安装文件
dpkg -S /path/to/file                      # 查找文件所属包
apt-cache depends package_name             # 查看依赖
apt-cache rdepends package_name            # 反向依赖
apt-cache policy package_name              # 查看版本来源

# 修复
sudo apt --fix-broken install
sudo dpkg --configure -a
sudo apt reinstall package_name
```

### 7.2 dpkg 包管理

```bash
sudo dpkg -i package.deb                   # 安装 .deb
sudo dpkg -r package_name                  # 卸载
sudo dpkg -P package_name                  # 彻底卸载
sudo dpkg -l                               # 已安装列表
dpkg -L package_name                       # 文件列表
dpkg -s package_name                       # 包状态
dpkg --audit                               # 检查不一致
```

### 7.3 Backports（获取新版软件）

```bash
# Debian 的特色：backports 仓库提供新版软件
# 添加 backports 源
echo "deb https://mirrors.aliyun.com/debian bookworm-backports main contrib non-free non-free-firmware" | sudo tee /etc/apt/sources.list.d/backports.list

sudo apt update

# 从 backports 安装
sudo apt install -t bookworm-backports package_name

# 查看可用的 backports 包
apt list -t bookworm-backports 2>/dev/null | grep backports

# 设置默认优先使用 backports（不推荐全局设置）
sudo nano /etc/apt/preferences.d/backports
# Package: *
# Pin: release a=bookworm-backports
# Pin-Priority: 100
```

### 7.4 第三方软件安装

```bash
# .deb 包
sudo dpkg -i package.deb
sudo apt install -f                     # 修复依赖

# 源码编译
tar -xzf source.tar.gz
cd source
./configure
make
sudo make install
sudo make uninstall

# Snap（需要手动安装）
sudo apt install -y snapd
sudo ln -s /var/lib/snapd/snap /snap
sudo snap install package_name

# Flatpak
sudo apt install -y flatpak
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install flathub package_name

# Python 包
pip3 install package_name
pip3 install --break-system-packages package_name   # Debian 12+

# 从源码安装 .deb
apt source package_name
cd package-*/
dpkg-buildpackage -us -uc
sudo dpkg -i ../package_*.deb
```

### 7.5 软件包固定（Pin）

```bash
# 防止某包被升级
sudo nano /etc/apt/preferences.d/hold-package
# Package: package_name
# Pin: version *
# Pin-Priority: -1

# 或使用 apt-mark
sudo apt-mark hold package_name
sudo apt-mark unhold package_name
apt-mark showhold

# 强制安装特定版本
sudo apt install package_name=version
```

### 7.6 构建自己的软件仓库

```bash
# 安装 reprepro
sudo apt install -y reprepro

# 创建仓库结构
mkdir -p /var/www/repository/conf
cat > /var/www/repository/conf/distributions << EOF
Origin: My Repository
Label: My Repository
Codename: bookworm
Architectures: amd64
Components: main
Description: My local repository
SignWith: YOUR_GPG_KEY_ID
EOF

# 添加包
cd /var/www/repository
reprepro includedeb bookworm /path/to/package.deb

# 配置客户端
echo "deb file:///var/www/repository bookworm main" | sudo tee /etc/apt/sources.list.d/local.list
```

---

## 八、网络配置与管理

### 8.1 网络信息查看

```bash
# IP 地址
ip addr show
ip a
ifconfig                       # 需安装 net-tools

# 路由
ip route show
route -n

# DNS
cat /etc/resolv.conf
resolvectl status              # systemd-resolved

# 连接
ss -tlnp                       # TCP 监听
ss -ulnp                       # UDP 监听
ss -tunap                      # 所有连接

# 流量
nload
iftop
nethogs
vnstat
```

### 8.2 网络配置方式

**方式一：/etc/network/interfaces（传统）**

```bash
sudo nano /etc/network/interfaces
```

**DHCP 配置：**
```
# 回环接口
auto lo
iface lo inet loopback

# 有线网络（DHCP）
auto eth0
iface eth0 inet dhcp
```

**静态 IP 配置：**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.168.1.100/24
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4 223.5.5.5
```

**WiFi 配置（使用 wpa_supplicant）：**
```
auto wlan0
iface wlan0 inet dhcp
    wpa-ssid "WiFi名称"
    wpa-psk "WiFi密码"
```

```bash
# 重启网络
sudo systemctl restart networking
sudo ifdown eth0 && sudo ifup eth0
```

**方式二：NetworkManager（桌面环境默认）**

```bash
# 查看连接
nmcli device status
nmcli connection show

# 修改连接
nmcli connection modify "Wired connection 1" ipv4.addresses 192.168.1.100/24
nmcli connection modify "Wired connection 1" ipv4.gateway 192.168.1.1
nmcli connection modify "Wired connection 1" ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection modify "Wired connection 1" ipv4.method manual

# 生效
nmcli connection up "Wired connection 1"

# WiFi
nmcli device wifi list
nmcli device wifi connect "SSID" password "password"
```

### 8.3 DNS 配置

```bash
# 传统方式（/etc/resolv.conf）
sudo nano /etc/resolv.conf
# nameserver 8.8.8.8
# nameserver 8.8.4.4
# nameserver 223.5.5.5

# 使用 resolvconf（防止被覆盖）
sudo apt install -y resolvconf
sudo nano /etc/resolvconf/resolv.conf.d/head
# nameserver 8.8.8.8
sudo resolvconf -u

# hosts 文件
sudo nano /etc/hosts
# 192.168.1.100   myserver.local

# DNS 查询
nslookup google.com
dig google.com
dig @8.8.8.8 google.com
host google.com
```

### 8.4 网络排查

```bash
# 连通性
ping -c 4 8.8.8.8
ping -c 4 google.com

# 路由追踪
traceroute google.com
mtr google.com

# 端口测试
nc -zv 192.168.1.100 22
nmap -p 22,80,443 192.168.1.100

# HTTP 测试
curl -I https://google.com
curl -v https://google.com

# 抓包
sudo tcpdump -i eth0 -nn
sudo tcpdump -i eth0 port 80 -nn -w capture.pcap
sudo wireshark

# 网卡信息
ip link show
ethtool eth0
```

### 8.5 防火墙（iptables / nftables）

**iptables（传统）：**
```bash
# 安装
sudo apt install -y iptables iptables-persistent

# 查看规则
sudo iptables -L -n -v

# 基本规则
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables -A INPUT -j DROP

# 保存规则
sudo netfilter-persistent save
```

**nftables（Debian 10+ 默认）：**
```bash
# 查看规则
sudo nft list ruleset

# 基本配置
sudo nano /etc/nftables.conf
```
```nft
#!/usr/sbin/nft -f
flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iif lo accept
        ct state established,related accept
        tcp dport { 22, 80, 443 } accept
    }

    chain forward {
        type filter hook forward priority 0; policy drop;
    }

    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```
```bash
# 启用
sudo systemctl enable nftables
sudo systemctl start nftables
```

**UFW（简化防火墙，可选）：**
```bash
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status verbose
```

### 8.6 代理配置

```bash
# 环境变量
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export no_proxy=localhost,127.0.0.1

# 永久配置
cat >> ~/.bashrc << 'EOF'
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export no_proxy=localhost,127.0.0.1
EOF

# APT 代理
echo 'Acquire::http::Proxy "http://127.0.0.1:7890";' | sudo tee /etc/apt/apt.conf.d/proxy

# Git 代理
git config --global http.proxy http://127.0.0.1:7890
```

---

## 九、服务与进程管理

### 9.1 systemd 服务管理

```bash
# 基本操作
sudo systemctl start service_name
sudo systemctl stop service_name
sudo systemctl restart service_name
sudo systemctl reload service_name
sudo systemctl status service_name
sudo systemctl enable service_name
sudo systemctl disable service_name
sudo systemctl is-active service_name
sudo systemctl is-enabled service_name

# 查看服务
systemctl list-units --type=service
systemctl list-units --type=service --state=running
systemctl list-units --type=service --state=failed
systemctl list-unit-files --type=service

# 日志
journalctl -u service_name
journalctl -u service_name -f
journalctl -u service_name --since "1 hour ago"
journalctl -u service_name -n 100
journalctl -p err
journalctl --disk-usage
sudo journalctl --vacuum-size=100M
```

### 9.2 自定义 systemd 服务

```bash
sudo nano /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Application
After=network.target
Wants=network.target

[Service]
Type=simple
User=www-data
Group=www-data
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
ExecStop=/bin/kill -s TERM $MAINPID
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl start myapp
sudo systemctl enable myapp
sudo systemctl status myapp
journalctl -u myapp -f
```

### 9.3 进程管理

```bash
# 查看进程
ps aux
ps -ef | grep nginx
ps aux --sort=-%mem | head
ps aux --sort=-%cpu | head
top
htop
pgrep -f "process_name"
pgrep -u username

# 管理进程
kill PID
kill -9 PID
killall process_name
pkill -f "pattern"

# 后台运行
command &
nohup command > output.log 2>&1 &
screen -S session_name
tmux new -s session_name
```

### 9.4 定时任务（Cron）

```bash
# 编辑当前用户的定时任务
crontab -e

# 查看
crontab -l

# 系统级定时任务
sudo nano /etc/crontab

# cron 格式
# ┌──────────── 分钟 (0-59)
# │ ┌────────── 小时 (0-23)
# │ │ ┌──────── 日 (1-31)
# │ │ │ ┌────── 月 (1-12)
# │ │ │ │ ┌──── 星期 (0-7, 0和7=周日)
# * * * * * command

# 常用示例
0 2 * * * /root/backup.sh              # 每天凌晨 2 点
*/5 * * * * /root/check.sh             # 每 5 分钟
0 0 * * 0 /root/weekly.sh              # 每周日
0 0 1 * * /root/monthly.sh             # 每月 1 号

# anacron（适合不常开机的桌面机）
sudo apt install -y anacron
```

### 9.5 init.d 脚本（传统 SysVinit）

```bash
# Debian 仍兼容 SysVinit 脚本
# 位置：/etc/init.d/
ls /etc/init.d/

# 管理
sudo /etc/init.d/service_name start
sudo /etc/init.d/service_name stop
sudo /etc/init.d/service_name restart

# 注册服务
sudo update-rc.d service_name defaults
sudo update-rc.d service_name remove

# 查看运行级别
runlevel
who -r
```

---

## 十、磁盘与存储管理

### 10.1 磁盘信息

```bash
df -h                           # 文件系统使用
df -i                           # inode 使用
du -sh /path                    # 目录大小
du -sh * | sort -hr             # 排序

lsblk                           # 块设备
sudo fdisk -l                   # 分区详情
sudo parted -l                  # 分区信息
blkid                           # 分区 UUID
```

### 10.2 分区管理

```bash
# fdisk（MBR）
sudo fdisk /dev/sdb
# n 创建、d 删除、p 打印、w 保存

# parted（GPT）
sudo parted /dev/sdb
# mklabel gpt
# mkpart primary ext4 0% 50%
# print / quit

# gparted（图形化）
sudo apt install -y gparted
sudo gparted

# 格式化
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.xfs /dev/sdb1
sudo mkfs.vfat /dev/sdb1
sudo mkfs.ntfs /dev/sdb1
sudo mkswap /dev/sdb2
```

### 10.3 挂载管理

```bash
# 手动挂载
sudo mount /dev/sdb1 /mnt/data
sudo mount -o ro /dev/sdb1 /mnt/data
sudo umount /mnt/data

# 永久挂载（/etc/fstab）
sudo blkid                            # 获取 UUID
sudo nano /etc/fstab
# UUID=xxxx-xxxx  /mnt/data  ext4  defaults  0  2

# 测试
sudo mount -a

# Swap 管理
sudo swapon /dev/sdb2
sudo swapoff /dev/sdb2
swapon --show
free -h
```

### 10.4 LVM 逻辑卷

```bash
# 安装
sudo apt install -y lvm2

# 创建物理卷
sudo pvcreate /dev/sdb1
sudo pvs

# 创建卷组
sudo vgcreate myvg /dev/sdb1
sudo vgs

# 创建逻辑卷
sudo lvcreate -L 20G -n mydata myvg
sudo lvcreate -l 100%FREE -n myhome myvg
sudo lvs

# 格式化并挂载
sudo mkfs.ext4 /dev/myvg/mydata
sudo mount /dev/myvg/mydata /mnt/data

# 扩容
sudo lvextend -L +10G /dev/myvg/mydata
sudo resize2fs /dev/myvg/mydata       # ext4
# sudo xfs_growfs /mnt/data            # XFS

# 缩容（ext4）
sudo umount /mnt/data
sudo e2fsck -f /dev/myvg/mydata
sudo resize2fs /dev/myvg/mydata 15G
sudo lvreduce -L 15G /dev/myvg/mydata
sudo mount /dev/myvg/mydata /mnt/data
```

### 10.5 RAID 配置

```bash
sudo apt install -y mdadm

# 创建 RAID 1
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

# 查看
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# 保存配置
sudo mdadm --detail --scan >> /etc/mdadm/mdadm.conf

# 格式化并挂载
sudo mkfs.ext4 /dev/md0
sudo mount /dev/md0 /mnt/raid

# 故障处理
sudo mdadm /dev/md0 --fail /dev/sdb1
sudo mdadm /dev/md0 --remove /dev/sdb1
sudo mdadm /dev/md0 --add /dev/sdd1
```

### 10.6 文件系统检查与修复

```bash
# 检查 ext4
sudo e2fsck -f /dev/sdb1

# 检查 XFS
sudo xfs_repair /dev/sdb1

# 查看文件系统信息
sudo tune2fs -l /dev/sdb1

# 调整 ext4 参数
sudo tune2fs -m 1 /dev/sdb1           # 减少预留空间为 1%
sudo tune2fs -c 30 /dev/sdb1          # 每 30 次挂载检查
```

---

## 十一、Shell 脚本编程

### 11.1 脚本基础

```bash
#!/bin/bash

# 变量
name="Debian"
version=12
echo "Hello, $name $version"

# 只读变量
readonly PI=3.14159

# 特殊变量
$0                              # 脚本名
$1 $2 $3                        # 参数
$#                              # 参数个数
$@                              # 所有参数
$?                              # 上一命令退出码
$$                              # 当前 PID

# 字符串操作
str="Hello, World"
echo ${#str}                    # 长度
echo ${str:0:5}                 # 截取
echo ${str/World/Bash}          # 替换
echo ${str^^}                   # 转大写
echo ${str,,}                   # 转小写

# 算术运算
a=10; b=3
echo $(( a + b ))
echo $(( a - b ))
echo $(( a * b ))
echo $(( a / b ))
echo $(( a % b ))
echo $(( a ** b ))              # 幂
(( a++ ))
```

### 11.2 条件判断

```bash
# if 语句
if [ "$x" -gt 10 ]; then
    echo "大于 10"
elif [ "$x" -eq 10 ]; then
    echo "等于 10"
else
    echo "小于 10"
fi

# 文件测试
[ -f file ]        # 是文件
[ -d dir ]         # 是目录
[ -e path ]        # 存在
[ -r file ]        # 可读
[ -w file ]        # 可写
[ -x file ]        # 可执行
[ -s file ]        # 非空

# 字符串测试
[ -z "$str" ]      # 为空
[ -n "$str" ]      # 非空
[ "$a" = "$b" ]    # 相等
[ "$a" != "$b" ]   # 不等

# 数值测试
[ "$a" -eq "$b" ]  # 等于
[ "$a" -ne "$b" ]  # 不等于
[ "$a" -gt "$b" ]  # 大于
[ "$a" -lt "$b" ]  # 小于
[ "$a" -ge "$b" ]  # 大于等于
[ "$a" -le "$b" ]  # 小于等于

# 逻辑运算
[ "$a" -gt 5 ] && [ "$b" -lt 10 ]    # 与
[ "$a" -gt 5 ] || [ "$b" -lt 10 ]    # 或

# 双括号 / 双方括号
if (( x > 10 )); then fi
if [[ "$str" == hello* ]]; then fi

# case
case "$option" in
    start)  echo "启动" ;;
    stop)   echo "停止" ;;
    *)      echo "用法: $0 {start|stop}" ; exit 1 ;;
esac
```

### 11.3 循环

```bash
# for
for i in 1 2 3 4 5; do echo $i; done
for i in {1..10}; do echo $i; done
for i in $(seq 1 10); do echo $i; done
for file in *.txt; do echo "$file"; done
for ((i=0; i<10; i++)); do echo $i; done

# while
while [ "$count" -lt 10 ]; do
    echo $count
    ((count++))
done

# 读取文件
while IFS= read -r line; do
    echo "$line"
done < file.txt

# until
until [ "$count" -ge 10 ]; do
    echo $count
    ((count++))
done

# break / continue
for i in {1..10}; do
    [ "$i" -eq 5 ] && continue
    [ "$i" -eq 8 ] && break
    echo $i
done
```

### 11.4 函数

```bash
greet() {
    local name="$1"
    echo "Hello, $name!"
}
greet "Alice"

# 返回值
add() {
    local result=$(( $1 + $2 ))
    echo $result
}
sum=$(add 3 4)

# 退出码
check_file() {
    [ -f "$1" ] && return 0 || return 1
}

# 递归
factorial() {
    if [ "$1" -le 1 ]; then
        echo 1
    else
        local prev=$(factorial $(( $1 - 1 )))
        echo $(( $1 * prev ))
    fi
}
```

### 11.5 数组

```bash
arr=(apple banana cherry)
echo ${arr[0]}                   # apple
echo ${arr[@]}                   # 所有
echo ${#arr[@]}                  # 长度
arr[3]="date"
arr+=("elderberry")

for item in "${arr[@]}"; do echo "$item"; done

# 关联数组
declare -A map
map[name]="Alice"
map[age]=25
echo ${map[name]}
echo ${!map[@]}                  # 所有键
```

### 11.6 实用脚本示例

**系统巡检脚本：**
```bash
#!/bin/bash
# system_check.sh
REPORT="/tmp/system_check_$(date +%Y%m%d_%H%M%S).txt"

{
echo "=== 系统巡检报告 ==="
echo "时间: $(date)"
echo "主机: $(hostname)"
echo ""
echo "=== 系统信息 ==="
uname -a
cat /etc/debian_version
echo ""
echo "=== CPU 负载 ==="
uptime
echo ""
echo "=== 内存 ==="
free -h
echo ""
echo "=== 磁盘 ==="
df -h | grep -E "^/dev"
echo ""
echo "=== 网络 ==="
ss -tlnp | head -10
echo ""
echo "=== 失败服务 ==="
systemctl list-units --state=failed --no-pager
echo ""
echo "=== 最近登录 ==="
last -5
} > "$REPORT" 2>&1

echo "报告已生成: $REPORT"
```

**自动备份脚本：**
```bash
#!/bin/bash
# backup.sh
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
SOURCE="/var/www/html"
DB_NAME="mydb"
RETENTION_DAYS=30

mkdir -p "$BACKUP_DIR"

# 备份文件
tar -czf "$BACKUP_DIR/web_${DATE}.tar.gz" "$SOURCE"

# 备份数据库
mysqldump -u root -p"$MYSQL_PASS" "$DB_NAME" | gzip > "$BACKUP_DIR/db_${DATE}.sql.gz"

# 清理旧备份
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +$RETENTION_DAYS -delete
find "$BACKUP_DIR" -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete

echo "备份完成: $DATE"
```

**日志分析脚本：**
```bash
#!/bin/bash
LOG="${1:-/var/log/syslog}"
echo "=== 日志分析: $LOG ==="
echo ""
echo "--- 错误统计 ---"
grep -ic "error" "$LOG"
echo ""
echo "--- Top 10 IP ---"
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' "$LOG" | sort | uniq -c | sort -rn | head -10
echo ""
echo "--- 最近错误 ---"
grep -i "error" "$LOG" | tail -10
```

---

## 十二、开发环境搭建

### 12.1 Git

```bash
sudo apt install -y git

git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main

# 基本操作
git init
git clone https://github.com/user/repo.git
git add .
git commit -m "message"
git push origin main
git pull origin main

# 分支
git branch feature
git checkout -b feature
git merge feature

# 查看
git status
git log --oneline --graph
git diff
```

### 12.2 Python

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev

# 虚拟环境
python3 -m venv venv
source venv/bin/activate
pip install package_name
pip freeze > requirements.txt

# pyenv（管理多版本）
curl https://pyenv.run | bash
# 添加到 ~/.bashrc
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

### 12.3 Node.js

```bash
# nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install --lts
nvm use 20

# 或 NodeSource
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# npm
npm install package_name
npm install -g package_name
```

### 12.4 Java

```bash
sudo apt install -y openjdk-17-jdk openjdk-17-jre

# 设置 JAVA_HOME
echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc

# Maven / Gradle
sudo apt install -y maven gradle

# SDKMAN
curl -s "https://get.sdkman.io" | bash
sdk install java 21-tem
sdk install maven
```

### 12.5 Docker

```bash
# 安装 Docker
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 允许普通用户使用
sudo usermod -aG docker $USER
newgrp docker

# 验证
docker --version
docker run hello-world

# 配置镜像加速
sudo nano /etc/docker/daemon.json
{
  "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
}
sudo systemctl restart docker
```

### 12.6 数据库

```bash
# MySQL / MariaDB
sudo apt install -y default-mysql-server     # MariaDB
sudo mysql_secure_installation

# PostgreSQL
sudo apt install -y postgresql postgresql-contrib
sudo -u postgres psql

# Redis
sudo apt install -y redis-server
redis-cli

# SQLite
sudo apt install -y sqlite3
```

### 12.7 编译工具链

```bash
# C/C++ 开发
sudo apt install -y build-essential cmake gdb valgrind

# 常用开发工具
sudo apt install -y pkg-config autoconf automake libtool

# 常用开发库
sudo apt install -y libssl-dev libcurl4-openssl-dev libxml2-dev libsqlite3-dev
```

---

## 十三、服务器运维

### 13.1 Web 服务器

**Nginx：**
```bash
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx

# 配置文件
/etc/nginx/nginx.conf
/etc/nginx/sites-available/
/etc/nginx/sites-enabled/

# 站点配置
sudo nano /etc/nginx/sites-available/mysite
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

**Apache：**
```bash
sudo apt install -y apache2
sudo systemctl enable apache2

# 配置
/etc/apache2/apache2.conf
/etc/apache2/sites-available/
/etc/apache2/sites-enabled/

sudo a2ensite mysite.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

### 13.2 SSL 证书

```bash
# Certbot
sudo apt install -y certbot python3-certbot-nginx

# Nginx 自动配置
sudo certbot --nginx -d example.com -d www.example.com

# Apache
sudo apt install -y python3-certbot-apache
sudo certbot --apache -d example.com

# 自动续期
sudo certbot renew --dry-run

# 查看
sudo certbot certificates
```

### 13.3 反向代理

```nginx
# Nginx 反向代理
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 13.4 邮件服务器

```bash
# Postfix（SMTP）
sudo apt install -y postfix
sudo dpkg-reconfigure postfix

# Dovecot（IMAP/POP3）
sudo apt install -y dovecot-imapd dovecot-pop3d

# 管理
sudo systemctl status postfix dovecot
```

### 13.5 DNS 服务器

```bash
# BIND9
sudo apt install -y bind9 bind9-utils bind9-doc

# 配置
sudo nano /etc/bind/named.conf.local
sudo nano /etc/bind/zones/db.example.com

sudo systemctl restart bind9
sudo systemctl enable bind9

# 测试
dig @localhost example.com
```

### 13.6 日志管理

```bash
# 系统日志
/var/log/syslog
/var/log/auth.log
/var/log/kern.log
/var/log/dpkg.log

# logrotate
/etc/logrotate.conf
/etc/logrotate.d/

# 自定义日志轮转
sudo nano /etc/logrotate.d/myapp
# /var/log/myapp/*.log {
#     daily
#     missingok
#     rotate 30
#     compress
#     notifempty
#     create 0640 myapp myapp
# }

# journalctl
journalctl -f
journalctl --since "2024-01-15"
```

---

## 十四、安全加固

### 14.1 系统更新策略

```bash
# 启用自动安全更新
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades

# 手动配置
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
# Unattended-Upgrade::Allowed-Origins {
#     "${distro_id}:${distro_codename}-security";
# };

sudo nano /etc/apt/apt.conf.d/20auto-upgrades
# APT::Periodic::Update-Package-Lists "1";
# APT::Periodic::Unattended-Upgrade "1";
```

### 14.2 SSH 安全加固

```bash
sudo nano /etc/ssh/sshd_config

Port 2222                       # 非默认端口
PermitRootLogin no              # 禁止 root
PasswordAuthentication no       # 仅密钥
MaxAuthTries 3
LoginGraceTime 30
ClientAliveInterval 300
AllowUsers username
X11Forwarding no

sudo systemctl restart ssh
```

### 14.3 防火墙配置

```bash
# UFW（推荐入门）
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable

# 或 nftables（Debian 10+ 默认）
sudo systemctl enable nftables
```

### 14.4 Fail2Ban

```bash
sudo apt install -y fail2ban

sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local

# [DEFAULT]
# bantime = 3600
# findtime = 600
# maxretry = 5

# [sshd]
# enabled = true
# port = 2222
# maxretry = 3

sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# 管理
sudo fail2ban-client status
sudo fail2ban-client status sshd
sudo fail2ban-client set sshd unbanip 192.168.1.100
```

### 14.5 其他安全措施

```bash
# 1. 禁用不需要的服务
sudo systemctl disable bluetooth cups avahi-daemon

# 2. 文件完整性检查
sudo apt install -y aide
sudo aideinit
sudo cp /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# 3. 审计
sudo apt install -y auditd
sudo auditctl -w /etc/passwd -p wa -k passwd_changes

# 4. 内核安全参数
sudo nano /etc/sysctl.d/99-security.conf
# net.ipv4.ip_forward = 0
# net.ipv4.conf.all.accept_redirects = 0
# kernel.randomize_va_space = 2
# fs.suid_dumpable = 0
sudo sysctl --system

# 5. 密码策略
sudo nano /etc/login.defs
# PASS_MAX_DAYS 90
# PASS_MIN_DAYS 7
# PASS_MIN_LEN 12

# 6. 查找 SUID 文件
sudo find / -perm -4000 -type f 2>/dev/null

# 7. Debian 安全公告
# 订阅 debian-security-announce 邮件列表
# https://www.debian.org/security/
```

---

## 十五、性能优化与监控

### 15.1 监控工具

```bash
# CPU
top / htop
mpstat 1
vmstat 1
sar -u 1

# 内存
free -h
vmstat 1

# 磁盘
iostat -xz 1
iotop
df -h

# 网络
iftop
nethogs
nload

# 综合
dstat
glances            # sudo apt install glances
nmon               # sudo apt install nmon
```

### 15.2 性能优化

```bash
# 内核参数优化
sudo nano /etc/sysctl.d/99-tuning.conf
```
```
# 网络
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_fin_timeout = 15

# 内存
vm.swappiness = 10
vm.dirty_ratio = 15

# 文件系统
fs.file-max = 2097152
fs.inotify.max_user_watches = 524288
```
```bash
sudo sysctl --system

# 文件描述符
sudo nano /etc/security/limits.conf
# * soft nofile 65535
# * hard nofile 65535

# I/O 调度器
cat /sys/block/sda/queue/scheduler
echo mq-deadline | sudo tee /sys/block/sda/queue/scheduler

# CPU 性能模式
sudo apt install -y cpufrequtils
sudo cpufreq-set -g performance
```

### 15.3 日志优化

```bash
sudo nano /etc/systemd/journald.conf
# SystemMaxUse=200M
# MaxRetentionSec=1month

sudo systemctl restart systemd-journald
sudo journalctl --vacuum-size=100M
```

---

## 十六、备份与恢复

### 16.1 rsync 备份

```bash
# 本地备份
rsync -avz /source/ /backup/

# 远程备份
rsync -avz /source/ user@remote:/backup/

# 排除文件
rsync -avz --delete \
    --exclude='.git' \
    --exclude='node_modules' \
    --exclude='*.log' \
    /source/ /backup/

# 增量备份（硬链接）
rsync -avz --delete \
    --link-dest=/backup/previous \
    /source/ /backup/current/

# 演练（不实际执行）
rsync -avzn /source/ /backup/
```

### 16.2 Timeshift 系统快照

```bash
sudo apt install -y timeshift

# 图形界面
timeshift-gtk

# 命令行
sudo timeshift --create --comments "安装完成"
sudo timeshift --list
sudo timeshift --restore
```

### 16.3 备份策略

```
3-2-1 备份原则：
- 3 份数据副本
- 2 种不同存储介质
- 1 份异地备份

计划：
├── 每日：增量备份（rsync / 数据库 dump）
├── 每周：完整备份
├── 每月：归档备份（异地）
└── 每次变更前：系统快照
```

### 16.4 数据库备份

```bash
# MySQL / MariaDB
mysqldump -u root -p database_name > backup.sql
mysqldump -u root -p --all-databases | gzip > all_backup.sql.gz

# 恢复
mysql -u root -p database_name < backup.sql

# PostgreSQL
pg_dump -U username database_name > backup.sql
pg_dumpall -U postgres > all_backup.sql

# 恢复
psql -U username database_name < backup.sql
```

---

## 十七、故障排查

### 17.1 启动问题

```bash
# 无法进入图形界面
# Ctrl + Alt + F2 → TTY 登录
sudo systemctl status gdm3       # 或 lightdm、sddm
sudo systemctl restart gdm3
sudo apt install --reinstall task-gnome-desktop

# GRUB 修复
# Live USB 启动
sudo mount /dev/sdaX /mnt
sudo mount /dev/sdaY /mnt/boot/efi   # 如有 EFI 分区
sudo grub-install --root-directory=/mnt /dev/sda
sudo chroot /mnt update-grub

# 恢复模式
# GRUB → Advanced options → Recovery mode
# 选项：clean、dpkg、fsck、grub、network、root shell

# init=/bin/bash（忘记 root 密码）
# GRUB 编辑启动项 → linux 行末尾加 init=/bin/bash
# mount -o remount,rw /
# passwd
# sync && exec /sbin/init
```

### 17.2 网络问题

```bash
ip addr show
ip route show
ping -c 4 8.8.8.8
ping -c 4 gateway_ip

# DNS
nslookup google.com
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# 网络服务
sudo systemctl restart networking
sudo systemctl restart NetworkManager

# 网卡
ip link show
sudo ip link set eth0 up
sudo dhclient eth0

# 防火墙
sudo iptables -L -n
sudo ufw status
```

### 17.3 磁盘问题

```bash
df -h
df -i                               # inode
du -sh /var/log/*
sudo journalctl --vacuum-size=100M
sudo apt autoremove
sudo apt clean

# 文件系统检查
sudo fsck /dev/sda1
sudo e2fsck -f /dev/sda1

# 只读问题
dmesg | grep -i error
sudo mount -o remount,rw /
```

### 17.4 软件问题

```bash
# 依赖问题
sudo apt --fix-broken install
sudo dpkg --configure -a

# GPG 错误
curl -fsSL https://archive.debian.org/debian/dists/$(cat /etc/debian_version | cut -d. -f1)/Release.gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/debian.gpg

# 软件包锁定
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/lib/apt/lists/lock
sudo dpkg --configure -a
```

### 17.5 常见错误速查

| 现象 | 可能原因 | 解决方案 |
|------|----------|----------|
| `Permission denied` | 权限不足 | `sudo` 或 `chmod` |
| `Command not found` | 未安装 | `apt install` |
| `No space left` | 磁盘满 | `df -h` 清理 |
| `Connection refused` | 服务未启动 | `systemctl start` |
| `Could not resolve host` | DNS | 修改 resolv.conf |
| `dpkg: error` | 依赖损坏 | `dpkg --configure -a` |
| `Unable to locate package` | 源问题 | `apt update`，检查 sources.list |
| `Failed to start service` | 配置错误 | `journalctl -u service` |
| `Boot error` | GRUB 损坏 | Live USB 修复 |
| `Frozen system` | 内存/IO | SysRq 强制重启 |

**Magic SysRq（强制恢复）：**
```bash
# 启用
echo 1 | sudo tee /proc/sys/kernel/sysrq

# 按键组合（Alt + SysRq + ...）
# R - 从 X 恢复键盘控制
# E - 终止所有进程（SIGTERM）
# I - 强制终止所有进程（SIGKILL）
# S - 同步文件系统
# U - 重新挂载为只读
# B - 重启

# 安全重启顺序：Alt+SysRq+R, E, I, S, U, B
```

---

## 十八、版本管理与升级

### 18.1 版本历史

| 版本 | 代号 | 发布时间 | 支持截止 | 类型 |
|------|------|----------|----------|------|
| Debian 9 | Stretch | 2017-06 | 2022-06 | EOL |
| Debian 10 | Buster | 2019-07 | 2024-06 | EOL |
| Debian 11 | Bullseye | 2021-08 | 2026-08 | Oldstable |
| Debian 12 | Bookworm | 2023-06 | 2028-06 | **Stable** |
| Debian 13 | Trixie | 预计 2025 | - | Testing |
| Debian - | Sid | 滚动 | - | Unstable |

### 18.2 查看版本

```bash
cat /etc/debian_version             # 如 12.5
cat /etc/os-release                  # 详细信息
hostnamectl
uname -r                             # 内核版本
uname -a
dpkg --print-architecture            # 架构
```

### 18.3 跨版本升级

```bash
# 升级前准备
# 1. 备份所有重要数据
# 2. 确保磁盘空间充足
# 3. 确保网络稳定
# 4. 阅读发行版升级说明

# 1. 更新当前系统到最新
sudo apt update && sudo apt full-upgrade -y

# 2. 修改 apt 源为新版本
sudo sed -i 's/bookworm/trixie/g' /etc/apt/sources.list
sudo sed -i 's/bookworm/trixie/g' /etc/apt/sources.list.d/*.list 2>/dev/null

# 3. 更新并升级
sudo apt update
sudo apt upgrade --without-new-pkgs     # 第一轮：不安装新包
sudo apt full-upgrade                   # 第二轮：完整升级

# 4. 清理
sudo apt autoremove --purge
sudo apt clean

# 5. 重启
sudo reboot

# 6. 验证
cat /etc/debian_version
```

### 18.4 内核管理

```bash
# 查看内核
uname -r

# 已安装内核
dpkg -l | grep linux-image

# 安装新内核
sudo apt install -y linux-image-amd64

# 删除旧内核
sudo apt autoremove --purge

# 查看启动参数
cat /proc/cmdline

# 修改 GRUB 默认启动项
sudo nano /etc/default/grub
# GRUB_DEFAULT=0
sudo update-grub
```

### 18.5 使用 Testing / Sid

```bash
# 添加 testing 源（不推荐用于生产）
sudo nano /etc/apt/sources.list
# deb https://mirrors.aliyun.com/debian trixie main contrib non-free non-free-firmware

# APT Pin 优先级控制
sudo nano /etc/apt/preferences.d/stable
# Package: *
# Pin: release a=stable
# Pin-Priority: 700
#
# Package: *
# Pin: release a=testing
# Pin-Priority: 650

# 从 testing 安装指定包
sudo apt install -t trixie package_name
```

---

## 十九、完整实战示例

### 19.1 Web 应用部署脚本

```bash
#!/bin/bash
# deploy_webapp.sh
set -e

APP_NAME="myapp"
APP_DIR="/opt/$APP_NAME"
NGINX_CONF="/etc/nginx/sites-available/$APP_NAME"
DOMAIN="example.com"

echo "=== 部署 $APP_NAME ==="

# 1. 安装依赖
echo "[1/6] 安装依赖..."
sudo apt update
sudo apt install -y nginx git python3 python3-pip python3-venv certbot python3-certbot-nginx

# 2. 克隆代码
echo "[2/6] 克隆代码..."
sudo mkdir -p "$APP_DIR"
sudo git clone https://github.com/user/$APP_NAME.git "$APP_DIR"

# 3. 配置 Python 环境
echo "[3/6] 配置 Python..."
cd "$APP_DIR"
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
deactivate

# 4. 创建 systemd 服务
echo "[4/6] 创建服务..."
sudo tee /etc/systemd/system/$APP_NAME.service > /dev/null << EOF
[Unit]
Description=$APP_NAME
After=network.target

[Service]
Type=simple
User=www-data
WorkingDirectory=$APP_DIR
ExecStart=$APP_DIR/venv/bin/gunicorn -w 4 -b 127.0.0.1:8000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable $APP_NAME
sudo systemctl start $APP_NAME

# 5. 配置 Nginx
echo "[5/6] 配置 Nginx..."
sudo tee "$NGINX_CONF" > /dev/null << EOF
server {
    listen 80;
    server_name $DOMAIN;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOF

sudo ln -sf "$NGINX_CONF" /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# 6. 配置 SSL
echo "[6/6] 配置 SSL..."
sudo certbot --nginx -d "$DOMAIN" --non-interactive --agree-tos --email admin@$DOMAIN

echo "=== 部署完成 ==="
echo "访问: https://$DOMAIN"
```

### 19.2 服务器初始化脚本

```bash
#!/bin/bash
# init_server.sh - Debian 服务器初始化
set -e

echo "=== Debian 服务器初始化 ==="

# 更新系统
echo "[1/9] 更新系统..."
sudo apt update && sudo apt full-upgrade -y

# 设置时区
echo "[2/9] 设置时区..."
sudo timedatectl set-timezone Asia/Shanghai

# 创建 sudo 用户
echo "[3/9] 配置 sudo 用户..."
read -p "输入新用户名: " NEW_USER
sudo adduser --gecos "" "$NEW_USER"
sudo usermod -aG sudo "$NEW_USER"

# 安装基础软件
echo "[4/9] 安装基础软件..."
sudo apt install -y \
    curl wget git vim nano htop tree unzip zip \
    net-tools openssh-server fail2ban ufw \
    build-essential \
    unattended-upgrades logrotate \
    ca-certificates gnupg lsb-release

# 配置防火墙
echo "[5/9] 配置防火墙..."
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw --force enable

# 加固 SSH
echo "[6/9] 加固 SSH..."
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/#MaxAuthTries 6/MaxAuthTries 3/' /etc/ssh/sshd_config
sudo systemctl restart ssh

# 配置 Fail2Ban
echo "[7/9] 配置 Fail2Ban..."
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# 配置自动更新
echo "[8/9] 配置自动安全更新..."
sudo dpkg-reconfigure -plow unattended-upgrades

# 优化内核
echo "[9/9] 优化内核参数..."
sudo tee /etc/sysctl.d/99-tuning.conf > /dev/null << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 1024 65535
vm.swappiness = 10
fs.file-max = 2097152
EOF
sudo sysctl --system

echo ""
echo "=== 初始化完成 ==="
echo "下一步："
echo "1. 重新登录: ssh $NEW_USER@$(hostname -I | awk '{print $1}')"
echo "2. 配置 SSH 密钥登录"
echo "3. 重启系统"
```

### 19.3 Docker 环境部署

```bash
#!/bin/bash
# setup_docker.sh
set -e

echo "=== 安装 Docker ==="

# 安装依赖
sudo apt install -y ca-certificates curl gnupg

# 添加 Docker GPG 密钥
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 添加仓库
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

# 安装
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 配置用户
sudo usermod -aG docker $USER

# 配置镜像加速
sudo tee /etc/docker/daemon.json > /dev/null << EOF
{
  "registry-mirrors": [
    "https://mirror.ccs.tencentyun.com",
    "https://docker.mirrors.ustc.edu.cn"
  ],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
EOF

sudo systemctl restart docker
sudo systemctl enable docker

echo "=== Docker 安装完成 ==="
docker --version
docker compose version
echo "请重新登录以使 docker 组生效"
```

---

## 附录 A：Debian 特有命令速查

| 命令 | 说明 |
|------|------|
| `tasksel` | 任务选择器（安装软件集合） |
| `apt-cache search` | 搜索软件包 |
| `apt-mark hold` | 锁定包版本 |
| `dpkg-reconfigure` | 重新配置已安装的包 |
| `update-alternatives` | 管理命令的多个版本 |
| `update-grub` | 更新 GRUB 配置 |
| `adduser` / `deluser` | Debian 风格用户管理 |
| `addgroup` / `delgroup` | Debian 风格组管理 |
| `netselect-apt` | 自动选择最快的镜像源 |
| `reportbug` | 报告 Debian Bug |
| `apt-listchanges` | 查看包变更日志 |

## 附录 B：推荐学习资源

### 书籍

| 书名 | 作者 | 方向 |
|------|------|------|
| 《Debian Administrator's Handbook》 | Raphaël Hertzog | Debian 管理（官方） |
| 《鸟哥的 Linux 私房菜》 | 鸟哥 | Linux 基础 |
| 《Linux 命令行与 Shell 脚本编程大全》 | Richard Blum | Shell 脚本 |
| 《Linux 性能优化实战》 | 倪朋飞 | 性能优化 |
| 《UNIX 环境高级编程》 | W. Richard Stevens | 系统编程 |

### 在线资源

| 资源 | 网址 | 说明 |
|------|------|------|
| Debian 官方文档 | https://www.debian.org/doc | 最权威 |
| Debian Wiki | https://wiki.debian.org | 知识库 |
| Debian 手册 | https://debian-handbook.info | 管理员手册 |
| Debian 论坛 | https://forums.debian.net | 社区 |
| Debian 安全 | https://www.debian.org/security | 安全公告 |
| Arch Wiki | https://wiki.archlinux.org | 通用 Linux 知识 |
| Linux Journey | https://linuxjourney.com | 交互学习 |
| DigitalOcean 教程 | https://www.digitalocean.com/community/tutorials | 实用教程 |

---

> **总结：** Debian 以稳定、安全、自由著称，是服务器和高级用户的首选 Linux 发行版。它的软件更新节奏较慢但极其可靠，非常适合需要长期稳定运行的生产环境。掌握 Debian 的包管理、网络配置和服务管理，你就拥有了驾驭大多数 Linux 系统的能力。
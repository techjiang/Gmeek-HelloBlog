> 全面涵盖安装、桌面使用、命令行、系统管理、网络配置、服务管理、开发环境搭建、Shell 脚本、安全加固、性能优化及故障排查。

---

## 目录

- [一、Ubuntu 简介](#一ubuntu-简介)
- [二、系统安装](#二系统安装)
- [三、初始配置](#三初始配置)
- [四、桌面环境与日常使用](#四桌面环境与日常使用)
- [五、命令行基础](#五命令行基础)
- [六、文件与目录管理](#六文件与目录管理)
- [七、用户与权限管理](#七用户与权限管理)
- [八、软件包管理](#八软件包管理)
- [九、网络配置与管理](#九网络配置与管理)
- [十、服务与进程管理](#十服务与进程管理)
- [十一、磁盘与存储管理](#十一磁盘与存储管理)
- [十二、Shell 脚本编程](#十二shell-脚本编程)
- [十三、开发环境搭建](#十三开发环境搭建)
- [十四、服务器运维](#十四服务器运维)
- [十五、安全加固](#十五安全加固)
- [十六、性能优化与监控](#十六性能优化与监控)
- [十七、备份与恢复](#十七备份与恢复)
- [十八、故障排查](#十八故障排查)
- [十九、Ubuntu 版本与升级](#十九ubuntu-版本与升级)
- [二十、完整实战示例](#二十完整实战示例)

---

## 一、Ubuntu 简介

### 什么是 Ubuntu

Ubuntu 是基于 Debian 的开源 Linux 发行版，由 Canonical 公司维护。它是全球最受欢迎的 Linux 发行版之一，广泛用于桌面、服务器、云计算和物联网。

### 版本类型

| 类型 | 版本号示例 | 支持周期 | 特点 |
|------|-----------|----------|------|
| **LTS（长期支持）** | 20.04、22.04、24.04 | 5 年（桌面/服务器） | 稳定、生产推荐 |
| **非 LTS** | 23.04、23.10、24.10 | 9 个月 | 新特性、尝鲜 |

> 版本号规则：`YY.MM`（年.月），如 `24.04` 表示 2024 年 4 月发布。

### 桌面环境

| 桌面环境 | 适合人群 | 特点 |
|----------|----------|------|
| **GNOME**（默认） | 普通用户、开发者 | 现代化、可扩展 |
| **KDE Plasma** | 追求定制 | 高度可定制、华丽 |
| **Xfce** | 低配机器 | 轻量、资源占用少 |
| **MATE** | 习惯传统界面 | 类似 GNOME 2 |
| **LXQt** | 极低配机器 | 最轻量 |

### 适用场景

- 桌面办公与日常使用
- Web / 数据库 / 应用服务器
- 云计算（AWS、Azure、GCP 首选）
- 开发工作站
- Docker / Kubernetes 容器平台
- IoT 嵌入式设备

### 官方资源

- 官网：https://ubuntu.com
- 文档：https://help.ubuntu.com
- 社区论坛：https://discourse.ubuntu.com
- Ask Ubuntu：https://askubuntu.com
- 软件包搜索：https://packages.ubuntu.com

---

## 二、系统安装

### 2.1 下载镜像

从官网下载：https://ubuntu.com/download/desktop

| 镜像 | 大小 | 用途 |
|------|------|------|
| Desktop ISO | ~5GB | 桌面安装（图形化） |
| Server ISO | ~2GB | 服务器安装（命令行） |
| Netboot | ~100MB | 网络安装 |
| Cloud Image | ~600MB | 云/虚拟机使用 |

**验证镜像完整性：**
```bash
# 校验 SHA256
sha256sum -c SHA256SUMS 2>&1 | grep -v 'FAILED' | grep OK

# 校验 GPG 签名（更安全）
gpg --verify SHA256SUMS.gpg SHA256SUMS
```

### 2.2 制作启动盘

**Linux 下使用 dd：**
```bash
# 确认 U 盘设备名
lsblk

# 制作启动盘（注意替换 /dev/sdX 为你的 U 盘）
sudo dd if=ubuntu-24.04-desktop-amd64.iso of=/dev/sdX bs=4M status=progress
sync
```

**Linux 下使用 Startup Disk Creator（图形化）：**
```
应用程序 → 启动盘创建工具 → 选择 ISO → 选择 U 盘 → 创建
```

**Windows 下使用 Rufus：**
```
1. 打开 Rufus
2. 选择 U 盘
3. 选择 Ubuntu ISO
4. 分区类型：GPT（UEFI）/ MBR（Legacy BIOS）
5. 点击"开始"
```

**跨平台 balenaEtcher：**
```
1. 打开 balenaEtcher
2. Flash from file → 选择 ISO
3. Select target → 选择 U 盘
4. Flash!
```

### 2.3 虚拟机安装（推荐入门）

#### VMware Workstation

```
1. 文件 → 新建虚拟机 → 典型
2. 选择"稍后安装操作系统"
3. 客户机操作系统：Linux → Ubuntu 64-bit
4. 命名虚拟机 → 选择存储位置
5. 磁盘大小：≥ 40GB（桌面）/ ≥ 20GB（服务器）
6. 自定义硬件：
   - 内存：≥ 4GB（推荐 8GB）
   - 处理器：≥ 2 核
   - 网络：NAT（推荐）或桥接
7. 选择 ISO 镜像 → 开启虚拟机
```

#### VirtualBox

```
1. 新建 → 名称：Ubuntu
2. 类型：Linux，版本：Ubuntu (64-bit)
3. 内存：≥ 4096MB
4. 创建虚拟硬盘：VDI，动态分配，≥ 40GB
5. 设置 → 存储 → 选择 ISO 镜像
6. 启动 → 进入安装
```

### 2.4 桌面版安装步骤

```
1. 选择语言 → 中文（简体）或 English
2. 选择"安装 Ubuntu"
3. 键盘布局：Chinese 或 English (US)
4. 安装类型：
   - 正常安装（推荐，包含常用软件）
   - 最小安装（仅浏览器和基础工具）
5. 更新选项：
   - ☑ 安装 Ubuntu 时下载更新
   - ☑ 为图形或 WiFi 硬件安装第三方软件
6. 分区方案：
   - 清除整个磁盘并安装 Ubuntu（最简单）
   - 其他选项（手动分区）
   - 与 Windows 共存（双系统）
7. 设置时区：Asia/Shanghai
8. 创建用户：用户名 + 密码
9. 等待安装完成 → 重启
```

**推荐手动分区方案：**
```
EFI 系统分区    512MB    fat32    /boot/efi（UEFI 引导）
交换分区        内存大小  swap     swap（内存 ≥ 16GB 可省略）
根分区          40-60GB  ext4     /
home 分区       剩余空间  ext4     /home（可选，方便重装）
```

### 2.5 服务器版安装步骤

```
1. 选择语言 → English
2. 选择"Install Ubuntu Server"
3. 键盘布局：English (US)
4. 网络配置：DHCP 或手动配置静态 IP
5. 代理：留空
6. 镜像源：使用默认或填入国内镜像源
7. 分区方案：
   - Use an entire disk（整个磁盘）
   - Custom storage layout（自定义分区）
8. 设置用户名和密码
9. 安装 OpenSSH server（勾选）
10. 选择要安装的软件（如 Docker、Kubernetes 等）
11. 安装完成 → 重启
```

### 2.6 WSL 安装（Windows 用户）

```powershell
# Windows 10/11 启用 WSL
wsl --install

# 或手动启用
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# 重启后
wsl --set-default-version 2

# 安装 Ubuntu
wsl --install -d Ubuntu-24.04

# 或从 Microsoft Store 安装
```

### 2.7 双系统安装（Windows + Ubuntu）

```
1. Windows 中压缩磁盘，留出 ≥ 60GB 未分配空间
2. BIOS/UEFI 设置：
   - 关闭 Secure Boot（Ubuntu 可能需要）
   - 关闭 Fast Boot
   - 设置从 U 盘启动
3. 安装 Ubuntu，选择"安装 Ubuntu，与 Windows 共存"
   或手动分区（在未分配空间上创建分区）
4. 安装 GRUB 引导（自动识别 Windows）
5. 重启后 GRUB 菜单可选择 Ubuntu 或 Windows
```

---

## 三、初始配置

### 3.1 系统更新

```bash
# 更新软件包索引
sudo apt update

# 升级所有软件包
sudo apt upgrade -y

# 完整升级（处理依赖变化、内核更新）
sudo apt full-upgrade -y

# 清理不需要的包
sudo apt autoremove -y
sudo apt autoclean

# 重启（内核更新后）
sudo reboot
```

### 3.2 更换国内镜像源

```bash
# 备份原文件
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

# Ubuntu 24.04 使用新格式（deb822）
# 编辑 /etc/apt/sources.list.d/ubuntu.sources
sudo nano /etc/apt/sources.list.d/ubuntu.sources

# 替换为阿里云源（以 24.04 noble 为例）
# Types: deb
# URIs: http://mirrors.aliyun.com/ubuntu/
# Suites: noble noble-updates noble-backports
# Components: main restricted universe multiverse
# Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

# 或使用一键更换脚本
sudo sed -i 's|http://archive.ubuntu.com|http://mirrors.aliyun.com|g' /etc/apt/sources.list
sudo sed -i 's|http://security.ubuntu.com|http://mirrors.aliyun.com|g' /etc/apt/sources.list

# 更新
sudo apt update
```

**常用国内镜像源：**
```
阿里云：http://mirrors.aliyun.com/ubuntu/
清华源：https://mirrors.tuna.tsinghua.edu.cn/ubuntu/
中科大：http://mirrors.ustc.edu.cn/ubuntu/
华为云：https://mirrors.huaweicloud.com/ubuntu/
网易源：http://mirrors.163.com/ubuntu/
```

### 3.3 安装基础软件

```bash
# 常用开发与系统工具
sudo apt install -y \
    build-essential \
    curl \
    wget \
    git \
    vim \
    nano \
    htop \
    tree \
    unzip \
    zip \
    net-tools \
    openssh-server \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release

# 中文输入法（Fcitx5）
sudo apt install -y fcitx5 fcitx5-chinese-addons

# 设置环境变量
cat >> ~/.profile << 'EOF'
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
EOF

# 中文字体
sudo apt install -y fonts-wqy-zenhei fonts-wqy-microhei fonts-noto-cjk

# 设置系统语言
sudo dpkg-reconfigure locales
# 选择 zh_CN.UTF-8 UTF-8
```

### 3.4 配置主机名

```bash
# 查看主机名
hostname

# 修改主机名（临时）
sudo hostname new-hostname

# 修改主机名（永久）
sudo hostnamectl set-hostname new-hostname

# 修改 /etc/hosts
sudo nano /etc/hosts
# 127.0.1.1    new-hostname
```

### 3.5 时间与时区

```bash
# 查看时区
timedatectl

# 设置时区
sudo timedatectl set-timezone Asia/Shanghai

# 同步时间（NTP）
sudo timedatectl set-ntp true

# 查看时间
date
date +"%Y-%m-%d %H:%M:%S"

# 手动设置时间
sudo date -s "2024-01-15 10:30:00"
```

### 3.6 SSH 配置

```bash
# 安装 SSH 服务
sudo apt install -y openssh-server

# 启动并设置开机自启
sudo systemctl enable ssh
sudo systemctl start ssh

# 查看状态
sudo systemctl status ssh

# 远程连接
ssh username@192.168.1.100

# SSH 密钥登录（更安全）
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh-copy-id username@192.168.1.100

# SSH 配置文件：~/.ssh/config
Host myserver
    HostName 192.168.1.100
    User username
    Port 22
    IdentityFile ~/.ssh/id_ed25519
# 之后可以用 ssh myserver 直接连接
```

**SSH 服务端安全配置（/etc/ssh/sshd_config）：**
```bash
sudo nano /etc/ssh/sshd_config

# 常用安全配置
Port 2222                       # 修改端口（减少扫描攻击）
PermitRootLogin no              # 禁止 root 直接登录
PasswordAuthentication no       # 仅允许密钥登录（更安全）
MaxAuthTries 3                  # 最大尝试次数
ClientAliveInterval 300         # 空闲超时
ClientAliveCountMax 2           # 超时次数

# 重启 SSH
sudo systemctl restart ssh
```

### 3.7 桌面环境优化

```bash
# 安装 GNOME 扩展工具
sudo apt install -y gnome-tweaks gnome-shell-extensions

# 安装常用桌面软件
sudo apt install -y \
    vlc \
    flameshot \
    terminator \
    gparted \
    timeshift \
    stacer \
    synaptic

# 安装 Chrome 浏览器
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/google-chrome.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/google-chrome.gpg] http://dl.google.com/linux/chrome/deb/ stable main" | sudo tee /etc/apt/sources.list.d/google-chrome.list
sudo apt update && sudo apt install -y google-chrome-stable

# 安装 VS Code
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /usr/share/keyrings/
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/vscode stable main" | sudo tee /etc/apt/sources.list.d/vscode.list
sudo apt update && sudo apt install -y code
```

---

## 四、桌面环境与日常使用

### 4.1 GNOME 桌面基础

```
常用快捷键：
Super（Win 键）        打开活动概览
Super + A             显示所有应用程序
Super + D             显示桌面
Super + L             锁屏
Super + ↑/↓/←/→      窗口最大化/还原/半屏
Ctrl + Alt + T        打开终端
Ctrl + Alt + D        显示桌面
Alt + Tab             切换窗口
Alt + `               切换同应用窗口
PrtSc                截图（全屏）
Shift + PrtSc         截图（区域）
Ctrl + Shift + PrtSc  截图（自定义）
```

### 4.2 文件管理器（Nautilus）

```
Ctrl + N              新建窗口
Ctrl + T              新建标签
Ctrl + H              显示/隐藏隐藏文件
Ctrl + L              编辑路径栏
Ctrl + Shift + N      新建文件夹
F2                    重命名
Delete                移到回收站
Ctrl + Delete         永久删除
Ctrl + A              全选
Ctrl + C/V/X          复制/粘贴/剪切
Ctrl + Z              撤销
Alt + ↑               返回上级目录
Alt + ←               返回上一页
```

### 4.3 软件安装方式

```
1. Ubuntu Software（图形化软件中心）
   - 最简单，适合新手
   - 搜索 → 安装

2. APT 命令行（推荐）
   - sudo apt install package_name

3. Snap 包
   - sudo snap install package_name
   - 自动更新，隔离运行

4. Flatpak
   - sudo apt install flatpak
   - flatpak install flathub package_name

5. .deb 包
   - sudo dpkg -i package.deb
   - sudo apt install -f

6. AppImage
   - chmod +x app.AppImage
   - ./app.AppImage

7. 源码编译
   - ./configure && make && sudo make install

8. PPA（个人软件源）
   - sudo add-apt-repository ppa:user/ppa-name
   - sudo apt update
   - sudo apt install package_name
```

### 4.4 常用桌面应用推荐

```
办公：LibreOffice、WPS Office、OnlyOffice
浏览器：Firefox（默认）、Google Chrome、Chromium
通讯：微信（Deepin Wine）、Telegram、Slack
媒体：VLC、Audacity、GIMP（图片编辑）、Inkscape（矢量图）
开发：VS Code、IntelliJ IDEA、Docker Desktop
虚拟机：VirtualBox、VMware Workstation Player
密码管理：KeePassXC
下载：aria2、Motrix
远程：Remmina（RDP/VNC/SSH）、TeamViewer
```

---

## 五、命令行基础

### 5.1 终端使用

```bash
# 打开终端
# 快捷键：Ctrl + Alt + T

# 常用快捷键
Ctrl + C              # 终止当前命令
Ctrl + Z              # 暂停任务（fg 恢复 / bg 后台）
Ctrl + D              # 退出终端 / 发送 EOF
Ctrl + R              # 搜索历史命令
Ctrl + L              # 清屏
Ctrl + A              # 光标移到行首
Ctrl + E              # 光标移到行尾
Ctrl + U              # 删除光标前内容
Ctrl + K              # 删除光标后内容
Ctrl + W              # 删除前一个单词
Ctrl + Y              # 粘贴已删除内容
Tab                   # 自动补全（按两次显示所有选项）
!!                    # 重复上一条命令
sudo !!               # 以 sudo 权限执行上一条命令
```

### 5.2 命令帮助

```bash
man command           # 查看手册（按 q 退出）
man -k keyword        # 搜索手册
command --help        # 查看简要帮助
info command          # 查看 info 文档
whatis command        # 简要说明
which command         # 查找命令位置
whereis command       # 查找命令相关文件
type command          # 查看命令类型
apropos keyword       # 搜索相关命令
```

### 5.3 Shell 类型

```bash
echo $SHELL           # 查看当前 Shell
cat /etc/shells       # 查看可用 Shell

# 安装 Zsh
sudo apt install -y zsh

# 安装 Oh My Zsh（美化 Zsh）
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 设置 Zsh 为默认 Shell
chsh -s $(which zsh)

# Oh My Zsh 常用插件
# git、zsh-autosuggestions、zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
# 编辑 ~/.zshrc → plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

---

## 六、文件与目录管理

### 6.1 文件系统结构

```
/                 根目录
├── bin           基本命令（所有用户可用）
├── boot          引导文件（内核、GRUB）
├── dev           设备文件
├── etc           系统配置文件
├── home          用户主目录
│   └── username  用户家目录
├── lib / lib64   系统库
├── media         可移动设备挂载点
├── mnt           临时挂载点
├── opt           第三方软件
├── proc          进程信息（虚拟）
├── root          root 用户家目录
├── run           运行时数据
├── sbin          系统管理命令
├── snap          Snap 包目录
├── srv           服务数据
├── sys           系统信息（虚拟）
├── tmp           临时文件
├── usr           用户程序
│   ├── bin       用户命令
│   ├── lib       库文件
│   ├── local     本地安装
│   ├── sbin      系统管理
│   └── share     共享数据
└── var           可变数据
    ├── cache     缓存
    ├── log       日志
    ├── lib       状态数据
    └── www       Web 数据
```

### 6.2 目录操作

```bash
pwd                   # 当前路径
ls                    # 列出文件
ls -lha               # 详细信息 + 隐藏文件 + 人类可读大小
ls -lt                # 按时间排序
ls -lS                # 按大小排序
ls -R                 # 递归列出
ls -d */              # 只列出目录

cd /path              # 切换目录
cd ~                  # 回到家目录
cd -                  # 回到上一个目录
cd ..                 # 上级目录
cd /                  # 根目录

mkdir dirname         # 创建目录
mkdir -p a/b/c        # 递归创建
mkdir dir1 dir2 dir3  # 同时创建多个

rmdir dirname         # 删除空目录
rm -rf dirname        # 强制递归删除（慎用！）
```

### 6.3 文件操作

```bash
touch file.txt        # 创建空文件 / 更新时间戳
cp file1 file2        # 复制文件
cp -a src/ dest/      # 递归复制（保留属性）
cp -r src/ dest/      # 递归复制目录
mv file1 file2        # 移动/重命名
mv file /path/        # 移动到目录
rm file.txt           # 删除文件
rm -i file.txt        # 交互式删除（确认）
rm -f file.txt        # 强制删除
ln -s /path/file link # 创建软链接
ln /path/file link    # 创建硬链接
```

### 6.4 文件查看

```bash
cat file.txt          # 显示全部内容
tac file.txt          # 反向显示
less file.txt         # 分页查看（/ 搜索，q 退出）
more file.txt         # 分页查看
head -n 20 file.txt   # 前 20 行
tail -n 20 file.txt   # 后 20 行
tail -f file.txt      # 实时追踪（看日志）
wc -l file.txt        # 统计行数
wc -w file.txt        # 统计单词数
wc -c file.txt        # 统计字节数
file file.txt         # 查看文件类型
stat file.txt         # 查看文件详细信息
```

### 6.5 查找文件

```bash
# find 命令（强大）
find /path -name "*.log"                  # 按名称查找
find /path -type f -size +100M            # 查找大于 100M 的文件
find /path -type d -name "src"            # 查找目录
find /path -mtime -7                      # 7 天内修改的文件
find /path -user username                 # 按用户查找
find /path -perm 755                      # 按权限查找
find /path -name "*.tmp" -delete          # 查找并删除
find /path -name "*.log" -exec grep "error" {} \;   # 查找并执行

# locate（快速，需先 updatedb）
sudo updatedb
locate filename

# which / whereis
which python3
whereis nginx
```

### 6.6 文本处理

```bash
# grep（搜索）
grep "keyword" file.txt
grep -r "keyword" /path/          # 递归搜索
grep -i "keyword" file.txt        # 忽略大小写
grep -n "keyword" file.txt        # 显示行号
grep -v "keyword" file.txt        # 反向匹配
grep -c "keyword" file.txt        # 统计匹配数
grep -E "error|warn" file.txt     # 正则（多模式）
grep -A 3 "error" file.txt        # 匹配行后 3 行
grep -B 3 "error" file.txt        # 匹配行前 3 行

# sed（流编辑）
sed 's/old/new/g' file.txt        # 全局替换
sed -i 's/old/new/g' file.txt     # 原地替换
sed -n '5,10p' file.txt           # 打印 5-10 行
sed '3d' file.txt                 # 删除第 3 行
sed '/^#/d' file.txt              # 删除注释行

# awk（文本分析）
awk '{print $1}' file.txt         # 打印第 1 列
awk -F: '{print $1}' /etc/passwd  # 指定分隔符
awk '$3 > 100' file.txt           # 条件过滤
awk '{sum+=$1} END {print sum}'   # 求和
awk 'NR==1{print}' file.txt       # 打印第 1 行

# sort / uniq
sort file.txt                     # 排序
sort -n file.txt                  # 数值排序
sort -r file.txt                  # 降序
sort -k2 file.txt                 # 按第 2 列排序
uniq file.txt                     # 去重（相邻）
sort file.txt | uniq -c           # 去重并计数

# cut
cut -d: -f1 /etc/passwd           # 按分隔符切割
cut -c1-10 file.txt               # 按字符切割

# tr（字符转换）
echo "hello" | tr 'a-z' 'A-Z'     # 转大写
cat file.txt | tr -d '\n'         # 删除换行
```

### 6.7 压缩与解压

```bash
# tar
tar -czvf a.tar.gz dir/           # 打包 gzip 压缩
tar -xzvf a.tar.gz                # 解压 .tar.gz
tar -xjvf a.tar.bz2               # 解压 .tar.bz2
tar -xJvf a.tar.xz                # 解压 .tar.xz
tar -tf a.tar.gz                  # 查看内容

# zip / unzip
zip -r a.zip dir/                 # 打包 zip
unzip a.zip                       # 解压 zip
unzip a.zip -d /path/             # 解压到指定目录

# gzip / gunzip
gzip file                         # 压缩
gunzip file.gz                    # 解压

# 7z
sudo apt install p7zip-full
7z x a.7z                         # 解压
7z a a.7z dir/                    # 压缩

# zstd（新格式，高效）
sudo apt install zstd
zstd file                         # 压缩
zstd -d file.zst                  # 解压
tar --zstd -cf a.tar.zst dir/     # tar + zstd
```

---

## 七、用户与权限管理

### 7.1 用户管理

```bash
# 创建用户
sudo adduser username             # 交互式创建（推荐）
sudo useradd -m -s /bin/bash username  # 命令式创建

# 修改密码
sudo passwd username              # 修改指定用户密码
passwd                            # 修改自己的密码

# 删除用户
sudo deluser username             # 删除用户
sudo userdel -r username          # 删除用户及家目录

# 修改用户信息
sudo usermod -l newname oldname   # 修改用户名
sudo usermod -d /new/home username  # 修改家目录
sudo usermod -s /bin/zsh username    # 修改默认 Shell
sudo usermod -aG sudo username    # 添加到 sudo 组
sudo usermod -G group1,group2 username  # 设置附加组

# 查看用户
whoami                            # 当前用户
who                               # 当前登录用户
w                                 # 详细登录信息
id username                       # UID/GID/组
groups username                   # 所属组
last                              # 登录历史
lastlog                           # 用户最后登录
cat /etc/passwd                   # 用户列表
cat /etc/group                    # 组列表
```

### 7.2 用户组管理

```bash
sudo addgroup groupname           # 创建组
sudo groupadd groupname           # 创建组
sudo delgroup groupname           # 删除组
sudo groupdel groupname           # 删除组
sudo usermod -aG groupname username  # 添加用户到组
sudo gpasswd -d username groupname   # 从组中移除用户
cat /etc/group | grep groupname   # 查看组成员
```

### 7.3 sudo 配置

```bash
# 编辑 sudo 配置（推荐使用 visudo）
sudo visudo

# 常用配置
username ALL=(ALL:ALL) ALL                # 允许 sudo（需密码）
username ALL=(ALL:ALL) NOPASSWD: ALL      # 免密码 sudo
%admin ALL=(ALL:ALL) ALL                  # admin 组所有成员
username ALL=(ALL) /usr/bin/apt, /bin/systemctl  # 只允许特定命令

# 配置 sudo 日志
sudo visudo
# Defaults logfile="/var/log/sudo.log"
```

### 7.4 文件权限

```bash
# 权限表示
# r(4) w(2) x(1)  所有者/组/其他
# 例：rwxr-xr-x = 755

# 修改权限
chmod 755 file                    # rwxr-xr-x
chmod 644 file                    # rw-r--r--
chmod +x script.sh                # 添加执行权限
chmod -R 755 directory/           # 递归修改

# 符号模式
chmod u+x file                    # 所有者添加执行权限
chmod g-w file                    # 组去掉写权限
chmod o=r file                    # 其他用户只读
chmod a+r file                    # 所有人添加读权限

# 修改所有者
sudo chown user:group file
sudo chown -R user:group directory/

# 修改组
sudo chgrp groupname file

# 特殊权限
chmod u+s file                    # SUID（以文件所有者身份执行）
chmod g+s directory               # SGID（新建文件继承组）
chmod +t directory                # Sticky bit（只有所有者能删除）

# umask（默认权限掩码）
umask                             # 查看
umask 022                         # 设置（新文件 644，新目录 755）

# 查看权限
ls -la file
stat file
getfacl file                      # 查看 ACL
setfacl -m u:username:rw file     # 设置 ACL
setfacl -x u:username file        # 删除 ACL
```

---

## 八、软件包管理

### 8.1 APT 包管理

```bash
# 基本操作
sudo apt update                              # 更新索引
sudo apt upgrade                             # 升级已安装包
sudo apt full-upgrade                        # 完整升级
sudo apt install package_name               # 安装
sudo apt install -y package_name            # 自动确认
sudo apt install package1 package2          # 安装多个
sudo apt remove package_name                # 卸载（保留配置）
sudo apt purge package_name                 # 彻底卸载
sudo apt autoremove                         # 清理不需要的依赖
sudo apt autoclean                          # 清理旧包缓存
sudo apt clean                              # 清理所有包缓存

# 查询
apt search keyword                           # 搜索
apt show package_name                        # 查看详情
apt list --installed                        # 已安装列表
apt list --upgradable                       # 可升级列表
apt list --all-versions package_name        # 所有版本
dpkg -l | grep keyword                      # 查询已安装
dpkg -L package_name                        # 查看安装的文件
dpkg -S /path/to/file                       # 查找文件属于哪个包
apt-cache depends package_name              # 查看依赖
apt-cache rdepends package_name             # 查看反向依赖

# 修复
sudo apt --fix-broken install              # 修复依赖
sudo dpkg --configure -a                    # 配置未完成的包
sudo apt reinstall package_name            # 重装
```

### 8.2 dpkg 包管理

```bash
sudo dpkg -i package.deb                   # 安装 .deb 包
sudo dpkg -r package_name                  # 卸载
sudo dpkg -P package_name                  # 彻底卸载
sudo dpkg -l                               # 已安装列表
dpkg -L package_name                       # 文件列表
dpkg -s package_name                       # 包状态
```

### 8.3 Snap 包管理

```bash
sudo snap install package_name             # 安装
sudo snap install package_name --classic   # 经典模式
sudo snap list                             # 已安装列表
sudo snap refresh                          # 更新所有
sudo snap refresh package_name             # 更新指定
sudo snap remove package_name              # 卸载
snap info package_name                     # 查看信息
snap find keyword                          # 搜索
```

### 8.4 Flatpak 包管理

```bash
sudo apt install flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

flatpak install flathub package_name
flatpak list
flatpak update
flatpak uninstall package_name
flatpak run package_name
```

### 8.5 PPA（个人软件包存档）

```bash
# 添加 PPA
sudo add-apt-repository ppa:user/ppa-name
sudo apt update

# 删除 PPA
sudo add-apt-repository --remove ppa:user/ppa-name

# 列出已添加的 PPA
ls /etc/apt/sources.list.d/
```

---

## 九、网络配置与管理

### 9.1 网络信息查看

```bash
# IP 地址
ip addr show                   # 推荐
ip a                           # 简写
ifconfig                       # 旧命令（需安装 net-tools）

# 路由表
ip route show
route -n

# DNS
cat /etc/resolv.conf
systemd-resolve --status

# 连接状态
ss -tlnp                       # TCP 监听端口
ss -ulnp                       # UDP 监听端口
ss -tunap                      # 所有连接
netstat -tlnp                  # 旧命令

# ARP 表
ip neigh show
arp -a

# 网络接口
ip link show
ethtool eth0                   # 网卡信息

# 流量监控
nload                          # 实时流量
iftop                          # 连接流量
nethogs                        # 按进程流量
vnstat                         # 流量统计
```

### 9.2 Netplan 网络配置（Ubuntu 18.04+）

```bash
# Netplan 配置文件位置
/etc/netplan/*.yaml

# 编辑配置
sudo nano /etc/netplan/01-netcfg.yaml
```

**DHCP 配置：**
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
      dhcp6: false
```

**静态 IP 配置：**
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
          - 223.5.5.5
```

**WiFi 配置：**
```yaml
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: true
      access-points:
        "WiFi名称":
          password: "WiFi密码"
```

**多网卡配置：**
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
    eth1:
      dhcp4: no
      addresses:
        - 10.0.0.1/24
```

```bash
# 应用配置
sudo netplan apply

# 调试配置
sudo netplan --debug apply

# 查看当前配置
sudo netplan status
```

### 9.3 DNS 配置

```bash
# 使用 systemd-resolved（默认）
sudo nano /etc/systemd/resolved.conf
# DNS=8.8.8.8 8.8.4.4 223.5.5.5
# FallbackDNS=114.114.114.114
sudo systemctl restart systemd-resolved

# 传统方式（/etc/resolv.conf）
# 注意：systemd-resolved 会覆盖此文件
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# hosts 文件
sudo nano /etc/hosts
# 192.168.1.100   myserver.local   myserver

# DNS 查询工具
nslookup google.com
dig google.com
dig +short google.com
dig @8.8.8.8 google.com         # 指定 DNS 服务器
host google.com
```

### 9.4 网络排查

```bash
# 连通性测试
ping -c 4 8.8.8.8
ping -c 4 google.com

# 路由追踪
traceroute google.com
mtr google.com                   # 实时路由追踪（更好）

# 端口测试
nc -zv 192.168.1.100 22         # 测试端口
telnet 192.168.1.100 80         # 测试端口（旧）
nmap -p 22,80,443 192.168.1.100 # 端口扫描

# HTTP 测试
curl -I https://google.com
curl -v https://google.com
wget --spider https://google.com

# 抓包
sudo tcpdump -i eth0 -nn
sudo tcpdump -i eth0 port 80 -nn
sudo tcpdump -i any -w capture.pcap
sudo wireshark                   # 图形化抓包
```

### 9.5 防火墙（UFW）

```bash
# UFW（Uncomplicated Firewall）
sudo apt install ufw

# 基本操作
sudo ufw enable                  # 启用防火墙
sudo ufw disable                 # 禁用防火墙
sudo ufw status                  # 查看状态
sudo ufw status verbose          # 详细状态
sudo ufw status numbered         # 编号显示

# 规则管理
sudo ufw allow 22/tcp            # 允许 SSH
sudo ufw allow 80/tcp            # 允许 HTTP
sudo ufw allow 443/tcp           # 允许 HTTPS
sudo ufw allow 3306/tcp          # 允许 MySQL
sudo ufw allow from 192.168.1.0/24  # 允许某个网段
sudo ufw allow from 192.168.1.100 to any port 3306  # 允许特定 IP
sudo ufw deny 23/tcp             # 拒绝 Telnet
sudo ufw delete allow 23/tcp     # 删除规则
sudo ufw delete 3                # 按编号删除规则

# 默认策略
sudo ufw default deny incoming   # 默认拒绝入站
sudo ufw default allow outgoing  # 默认允许出站

# 应用配置
sudo ufw app list                # 可用应用
sudo ufw allow 'OpenSSH'         # 按应用名允许
sudo ufw allow 'Nginx Full'      # 允许 Nginx（80+443）
```

### 9.6 代理配置

```bash
# 环境变量
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export no_proxy=localhost,127.0.0.1,10.0.0.0/8

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
git config --global https.proxy http://127.0.0.1:7890

# pip 代理
pip install --proxy http://127.0.0.1:7890 package_name
```

---

## 十、服务与进程管理

### 10.1 systemd 服务管理

```bash
# 服务管理
sudo systemctl start service_name       # 启动
sudo systemctl stop service_name        # 停止
sudo systemctl restart service_name     # 重启
sudo systemctl reload service_name      # 重载配置
sudo systemctl status service_name      # 查看状态
sudo systemctl enable service_name      # 开机自启
sudo systemctl disable service_name     # 取消自启
sudo systemctl is-active service_name   # 是否运行中
sudo systemctl is-enabled service_name  # 是否开机自启

# 查看服务
systemctl list-units --type=service                    # 所有服务
systemctl list-units --type=service --state=running    # 运行中的服务
systemctl list-units --type=service --state=failed     # 失败的服务

# 日志（journalctl）
journalctl -u service_name              # 某服务日志
journalctl -u service_name -f           # 实时查看
journalctl -u service_name --since "1 hour ago"    # 最近 1 小时
journalctl -u service_name -n 100       # 最近 100 行
journalctl -p err                       # 错误级别日志
journalctl --disk-usage                 # 日志占用空间
sudo journalctl --vacuum-size=100M      # 清理日志到 100M
```

### 10.2 自定义 systemd 服务

```bash
# 创建服务文件
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
Environment=PATH=/usr/local/bin:/usr/bin:/bin

[Install]
WantedBy=multi-user.target
```

```bash
# 重新加载 systemd
sudo systemctl daemon-reload

# 启动服务
sudo systemctl start myapp
sudo systemctl enable myapp

# 查看状态
sudo systemctl status myapp
journalctl -u myapp -f
```

### 10.3 进程管理

```bash
# 查看进程
ps aux                          # 所有进程
ps -ef | grep nginx             # 查找进程
ps aux --sort=-%mem | head      # 按内存排序
ps aux --sort=-%cpu | head      # 按 CPU 排序
top                             # 实时监控
htop                            # 更好的监控工具
pgrep -f "process_name"         # 按名称查找 PID
pgrep -u username               # 按用户查找 PID

# 管理进程
kill PID                        # 终止进程
kill -9 PID                     # 强制终止
kill -15 PID                    # 优雅终止（默认）
killall process_name            # 按名称终止
pkill -f "pattern"              # 按模式终止

# 后台运行
command &                       # 后台运行
nohup command > output.log 2>&1 &   # 后台运行，不受终端影响
screen -S session_name          # screen 会话
tmux new -s session_name        # tmux 会话

# tmux 常用操作
# Ctrl+B D      分离会话
# tmux attach -t session_name   重新连接
# Ctrl+B C      新建窗口
# Ctrl+B N      下一个窗口
# Ctrl+B %      垂直分屏
# Ctrl+B "      水平分屏
```

---

## 十一、磁盘与存储管理

### 11.1 磁盘信息查看

```bash
# 磁盘使用
df -h                           # 文件系统使用情况
df -i                           # inode 使用情况
du -sh /path                    # 目录大小
du -sh * | sort -hr             # 当前目录各文件大小排序
du -sh /var/log/*               # 日志目录大小

# 磁盘分区
lsblk                           # 块设备列表
sudo fdisk -l                   # 分区详情
sudo parted -l                  # 分区信息
blkid                           # 分区 UUID

# 磁盘性能
sudo hdparm -Tt /dev/sda        # 磁盘速度测试
sudo iotop                      # IO 监控
iostat -xz 1                    # IO 统计
```

### 11.2 分区管理

```bash
# 使用 fdisk（MBR）
sudo fdisk /dev/sdb
# n 创建分区
# d 删除分区
# p 打印分区表
# w 保存退出

# 使用 parted（GPT）
sudo parted /dev/sdb
# mklabel gpt
# mkpart primary ext4 0% 50%
# print
# quit

# 使用 gparted（图形化）
sudo gparted

# 格式化分区
sudo mkfs.ext4 /dev/sdb1        # ext4
sudo mkfs.xfs /dev/sdb1         # XFS
sudo mkfs.vfat /dev/sdb1        # FAT32
sudo mkfs.ntfs /dev/sdb1        # NTFS
sudo mkswap /dev/sdb2           # swap
```

### 11.3 挂载管理

```bash
# 手动挂载
sudo mount /dev/sdb1 /mnt/data
sudo mount -o ro /dev/sdb1 /mnt/data     # 只读挂载
sudo umount /mnt/data                     # 卸载

# 永久挂载（/etc/fstab）
sudo blkid                              # 获取 UUID
sudo nano /etc/fstab
# UUID=xxxx-xxxx  /mnt/data  ext4  defaults  0  2

# 测试 fstab（不重启）
sudo mount -a

# Swap 管理
sudo swapon /dev/sdb2
sudo swapoff /dev/sdb2
swapon --show
free -h
```

### 11.4 LVM 逻辑卷管理

```bash
# 创建物理卷
sudo pvcreate /dev/sdb1
sudo pvs                          # 查看物理卷

# 创建卷组
sudo vgcreate myvg /dev/sdb1
sudo vgs                          # 查看卷组

# 创建逻辑卷
sudo lvcreate -L 20G -n mydata myvg
sudo lvcreate -l 100%FREE -n myhome myvg
sudo lvs                          # 查看逻辑卷

# 格式化并挂载
sudo mkfs.ext4 /dev/myvg/mydata
sudo mount /dev/myvg/mydata /mnt/data

# 扩容
sudo lvextend -L +10G /dev/myvg/mydata
sudo resize2fs /dev/myvg/mydata     # ext4
# sudo xfs_growfs /mnt/data          # XFS

# 缩容（ext4）
sudo umount /mnt/data
sudo e2fsck -f /dev/myvg/mydata
sudo resize2fs /dev/myvg/mydata 15G
sudo lvreduce -L 15G /dev/myvg/mydata
sudo mount /dev/myvg/mydata /mnt/data
```

### 11.5 RAID 配置

```bash
# 安装 mdadm
sudo apt install mdadm

# 创建 RAID 1（镜像）
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

# 查看状态
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# 保存配置
sudo mdadm --detail --scan >> /etc/mdadm/mdadm.conf

# 格式化并挂载
sudo mkfs.ext4 /dev/md0
sudo mount /dev/md0 /mnt/raid

# 故障恢复
sudo mdadm /dev/md0 --fail /dev/sdb1    # 标记故障
sudo mdadm /dev/md0 --remove /dev/sdb1  # 移除
sudo mdadm /dev/md0 --add /dev/sdd1     # 替换
```

---

## 十二、Shell 脚本编程

### 12.1 脚本基础

```bash
#!/bin/bash
# 脚本开头必须指定解释器

# 变量
name="Ubuntu"
version=24.04
echo "Hello, $name $version"

# 只读变量
readonly PI=3.14159

# 环境变量
export MY_VAR="value"

# 字符串操作
str="Hello, World"
echo ${#str}                    # 长度
echo ${str:0:5}                 # 截取
echo ${str/World/Bash}          # 替换
echo ${str^^}                   # 转大写
echo ${str,,}                   # 转小写

# 特殊变量
$0                              # 脚本名
$1 $2 $3                        # 参数
$#                              # 参数个数
$@                              # 所有参数
$*                              # 所有参数（字符串）
$?                              # 上一命令退出码
$$                              # 当前进程 PID
$!                              # 后台进程 PID

# 字符串拼接
greeting="Hello"
name="World"
full="$greeting, $name!"
```

### 12.2 条件判断

```bash
# if 语句
if [ "$x" -gt 10 ]; then
    echo "大于 10"
elif [ "$x" -eq 10 ]; then
    echo "等于 10"
else
    echo "小于 10"
fi

# 常用测试条件
# 文件测试
[ -f file ]                     # 是否为文件
[ -d dir ]                      # 是否为目录
[ -e path ]                     # 是否存在
[ -r file ]                     # 是否可读
[ -w file ]                     # 是否可写
[ -x file ]                     # 是否可执行
[ -s file ]                     # 是否非空
[ -L link ]                     # 是否为软链接

# 字符串测试
[ -z "$str" ]                   # 是否为空
[ -n "$str" ]                   # 是否非空
[ "$a" = "$b" ]                 # 是否相等
[ "$a" != "$b" ]                # 是否不等

# 数值测试
[ "$a" -eq "$b" ]              # 等于
[ "$a" -ne "$b" ]              # 不等于
[ "$a" -gt "$b" ]              # 大于
[ "$a" -lt "$b" ]              # 小于
[ "$a" -ge "$b" ]              # 大于等于
[ "$a" -le "$b" ]              # 小于等于

# 逻辑运算
[ "$a" -gt 5 ] && [ "$b" -lt 10 ]    # 与
[ "$a" -gt 5 ] || [ "$b" -lt 10 ]    # 或
[ ! "$a" -gt 5 ]                      # 非

# 双括号（支持算术）
if (( x > 10 && y < 20 )); then
    echo "条件成立"
fi

# 双方括号（支持模式匹配）
if [[ "$str" == hello* ]]; then
    echo "以 hello 开头"
fi

# case 语句
case "$option" in
    start)
        echo "启动"
        ;;
    stop)
        echo "停止"
        ;;
    restart)
        echo "重启"
        ;;
    *)
        echo "用法: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

### 12.3 循环

```bash
# for 循环
for i in 1 2 3 4 5; do
    echo $i
done

for i in {1..10}; do
    echo $i
done

for i in $(seq 1 10); do
    echo $i
done

for file in *.txt; do
    echo "$file"
done

for i in {1..10..2}; do          # 步长为 2
    echo $i
done

# C 风格 for
for ((i=0; i<10; i++)); do
    echo $i
done

# while 循环
while [ "$count" -lt 10 ]; do
    echo $count
    ((count++))
done

# 读取文件
while IFS= read -r line; do
    echo "$line"
done < file.txt

# 读取命令输出
while IFS= read -r line; do
    echo "$line"
done < <(command)

# until 循环
until [ "$count" -ge 10 ]; do
    echo $count
    ((count++))
done

# break / continue
for i in {1..10}; do
    [ "$i" -eq 5 ] && continue   # 跳过 5
    [ "$i" -eq 8 ] && break      # 到 8 停止
    echo $i
done

# 嵌套循环
for i in {1..3}; do
    for j in {1..3}; do
        echo "$i,$j"
    done
done
```

### 12.4 函数

```bash
# 定义函数
greet() {
    local name="$1"              # local 限定局部变量
    echo "Hello, $name!"
}

# 调用函数
greet "Alice"

# 带返回值的函数
add() {
    local result=$(( $1 + $2 ))
    echo $result
}
sum=$(add 3 4)                   # 通过命令捕获返回值
echo "Sum: $sum"

# 退出码
check_file() {
    if [ -f "$1" ]; then
        return 0                 # 成功
    else
        return 1                 # 失败
    fi
}

if check_file "/etc/hosts"; then
    echo "文件存在"
fi

# 递归函数
factorial() {
    if [ "$1" -le 1 ]; then
        echo 1
    else
        local prev=$(factorial $(( $1 - 1 )))
        echo $(( $1 * prev ))
    fi
}
echo $(factorial 5)              # 120
```

### 12.5 数组

```bash
# 一维数组
arr=(apple banana cherry)

# 访问
echo ${arr[0]}                   # apple
echo ${arr[@]}                   # 所有元素
echo ${#arr[@]}                  # 长度
echo ${#arr[0]}                  # 第一个元素长度

# 添加元素
arr[3]="date"
arr+=("elderberry")

# 遍历
for item in "${arr[@]}"; do
    echo "$item"
done

# 删除元素
unset arr[1]

# 切片
echo ${arr[@]:1:2}               # 从索引 1 开始取 2 个

# 关联数组（字典）
declare -A map
map[name]="Alice"
map[age]=25
echo ${map[name]}
echo ${!map[@]}                  # 所有键
echo ${map[@]}                   # 所有值
```

### 12.6 实用脚本示例

**批量重命名：**
```bash
#!/bin/bash
# 将当前目录所有 .txt 文件改为 .md
for file in *.txt; do
    [ -f "$file" ] || continue
    newname="${file%.txt}.md"
    mv "$file" "$newname"
    echo "Renamed: $file → $newname"
done
```

**日志分析：**
```bash
#!/bin/bash
# 分析 Nginx 访问日志
LOG_FILE="/var/log/nginx/access.log"

echo "=== 日志分析报告 ==="
echo "总请求数: $(wc -l < "$LOG_FILE")"
echo ""
echo "Top 10 IP:"
awk '{print $1}' "$LOG_FILE" | sort | uniq -c | sort -rn | head -10
echo ""
echo "状态码分布:"
awk '{print $9}' "$LOG_FILE" | sort | uniq -c | sort -rn
echo ""
echo "Top 10 URL:"
awk '{print $7}' "$LOG_FILE" | sort | uniq -c | sort -rn | head -10
```

**系统健康检查：**
```bash
#!/bin/bash
echo "=== 系统健康检查 ==="
echo "时间: $(date)"
echo "主机名: $(hostname)"
echo "系统: $(lsb_release -ds)"
echo "内核: $(uname -r)"
echo ""
echo "=== CPU 负载 ==="
uptime
echo ""
echo "=== 内存使用 ==="
free -h
echo ""
echo "=== 磁盘使用 ==="
df -h | grep -E "^/dev"
echo ""
echo "=== 网络连接 ==="
ss -tlnp | head -10
echo ""
echo "=== 最近登录 ==="
last -5
echo ""
echo "=== 失败服务 ==="
systemctl list-units --state=failed
```

**自动备份脚本：**
```bash
#!/bin/bash
# 每日备份脚本
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
SOURCE="/var/www/html"
DB_NAME="mydb"

mkdir -p "$BACKUP_DIR"

# 备份文件
tar -czf "$BACKUP_DIR/web_${DATE}.tar.gz" "$SOURCE"

# 备份数据库
mysqldump -u root -p"$MYSQL_PASS" "$DB_NAME" | gzip > "$BACKUP_DIR/db_${DATE}.sql.gz"

# 清理 30 天前的备份
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete
find "$BACKUP_DIR" -name "*.sql.gz" -mtime +30 -delete

echo "备份完成: $DATE"
```

---

## 十三、开发环境搭建

### 13.1 Git 版本控制

```bash
# 安装
sudo apt install -y git

# 基本配置
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
git config --global core.editor vim

# 基本操作
git init                          # 初始化仓库
git clone https://github.com/user/repo.git  # 克隆
git add .                         # 添加到暂存区
git commit -m "message"           # 提交
git push origin main              # 推送
git pull origin main              # 拉取
git fetch                         # 获取更新

# 分支管理
git branch                        # 查看分支
git branch feature                # 创建分支
git checkout feature              # 切换分支
git checkout -b feature           # 创建并切换
git merge feature                 # 合并分支
git branch -d feature             # 删除分支

# 查看
git status                        # 状态
git log --oneline --graph         # 日志
git diff                          # 差异
git show HEAD                     # 最近提交

# 撤销
git checkout -- file              # 撤销修改
git reset HEAD file               # 取消暂存
git reset --hard HEAD~1           # 回退一个提交
git revert commit_id              # 撤销某次提交

# 标签
git tag v1.0.0
git push origin v1.0.0
```

### 13.2 Python 开发环境

```bash
# 安装 Python
sudo apt install -y python3 python3-pip python3-venv python3-dev

# 创建虚拟环境
python3 -m venv venv
source venv/bin/activate
deactivate                        # 退出虚拟环境

# pip 常用
pip install package_name
pip install -r requirements.txt
pip freeze > requirements.txt
pip list
pip install --upgrade pip

# 安装 pyenv（管理多版本）
curl https://pyenv.run | bash
# 添加到 ~/.bashrc
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# 安装 conda（Anaconda/Miniconda）
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh

# 代码质量工具
pip install black flake8 mypy pytest
```

### 13.3 Node.js 开发环境

```bash
# 方式一：使用 nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install --lts                 # 安装 LTS 版本
nvm install 20                    # 安装指定版本
nvm use 20                        # 切换版本
nvm ls                            # 查看版本列表

# 方式二：使用 NodeSource
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# npm 常用
npm install package_name          # 安装
npm install -g package_name       # 全局安装
npm init -y                       # 初始化项目
npm run dev                       # 运行脚本
npm list                          # 查看依赖

# 使用 pnpm（更快的包管理器）
npm install -g pnpm
pnpm install
```

### 13.4 Java 开发环境

```bash
# 安装 JDK
sudo apt install -y openjdk-17-jdk openjdk-17-jre

# 查看版本
java -version
javac -version

# 设置 JAVA_HOME
echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# 安装 Maven
sudo apt install -y maven

# 安装 Gradle
sudo apt install -y gradle
# 或使用 SDKMAN
curl -s "https://get.sdkman.io" | bash
sdk install gradle

# 多版本管理
sudo update-alternatives --config java
```

### 13.5 Docker 安装

```bash
# 安装 Docker
# 方式一：官方脚本（推荐）
curl -fsSL https://get.docker.com | sudo sh

# 方式二：手动安装
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 允许普通用户使用 Docker
sudo usermod -aG docker $USER
newgrp docker

# 验证
docker --version
docker run hello-world

# Docker Compose
docker compose version

# 配置镜像加速（/etc/docker/daemon.json）
sudo nano /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://mirror.ccs.tencentyun.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
sudo systemctl restart docker

# 常用 Docker 命令
docker ps                        # 运行中的容器
docker ps -a                     # 所有容器
docker images                    # 镜像列表
docker pull nginx                # 拉取镜像
docker run -d -p 8080:80 nginx   # 运行容器
docker exec -it container_id bash  # 进入容器
docker logs container_id         # 查看日志
docker stop container_id         # 停止
docker rm container_id           # 删除容器
docker system prune -a           # 清理
```

### 13.6 数据库

```bash
# MySQL
sudo apt install -y mysql-server
sudo mysql_secure_installation
sudo systemctl status mysql

# PostgreSQL
sudo apt install -y postgresql postgresql-contrib
sudo -u postgres psql
# CREATE USER myuser WITH PASSWORD 'mypass';
# CREATE DATABASE mydb OWNER myuser;

# Redis
sudo apt install -y redis-server
sudo systemctl enable redis-server
redis-cli

# MongoDB
wget -qO - https://www.mongodb.org/static/pgp/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb.gpg
echo "deb [ signed-by=/usr/share/keyrings/mongodb.gpg ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update && sudo apt install -y mongodb-org

# SQLite
sudo apt install -y sqlite3
```

### 13.7 VS Code 配置

```bash
# 安装 VS Code（前面已述）
# 或使用 snap
sudo snap install code --classic

# 常用扩展
# Python、Pylance、Docker、GitLens、Remote-SSH
# C/C++、Java Extension Pack、ESLint、Prettier

# Settings Sync（设置同步）
# 登录 GitHub 同步设置
```

---

## 十四、服务器运维

### 14.1 Web 服务器

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
# server {
#     listen 80;
#     server_name example.com;
#     root /var/www/example;
#     index index.html;
# }

sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo nginx -t                    # 检查配置
sudo systemctl reload nginx
```

**Apache：**
```bash
sudo apt install -y apache2
sudo systemctl enable apache2

# 配置文件
/etc/apache2/apache2.conf
/etc/apache2/sites-available/
/etc/apache2/sites-enabled/

# 站点配置
sudo nano /etc/apache2/sites-available/mysite.conf
sudo a2ensite mysite.conf
sudo a2enmod rewrite
sudo systemctl reload apache2
```

### 14.2 SSL 证书（Let's Encrypt）

```bash
# Certbot（自动获取和续期证书）
sudo apt install -y certbot python3-certbot-nginx

# Nginx 自动配置
sudo certbot --nginx -d example.com -d www.example.com

# Apache 自动配置
sudo apt install -y python3-certbot-apache
sudo certbot --apache -d example.com

# 仅获取证书
sudo certbot certonly --standalone -d example.com

# 自动续期测试
sudo certbot renew --dry-run

# 查看证书
sudo certbot certificates
```

### 14.3 反向代理配置

```nginx
# Nginx 反向代理示例
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

### 14.4 定时任务（Cron）

```bash
# 编辑定时任务
crontab -e

# 查看定时任务
crontab -l

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
30 22 * * 1-5 /root/workday.sh         # 工作日 22:30

# 系统级定时任务
sudo nano /etc/crontab
sudo systemctl status cron

# anacron（适合不常开机的机器）
sudo apt install anacron
```

### 14.5 日志管理

```bash
# 系统日志
/var/log/syslog                   # 系统日志
/var/log/auth.log                 # 认证日志
/var/log/kern.log                 # 内核日志
/var/log/dpkg.log                 # 软件包日志
/var/log/apt/history.log          # APT 历史

# logrotate（日志轮转）
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

# 查看日志
tail -f /var/log/syslog
journalctl -f
journalctl --since "2024-01-15" --until "2024-01-16"
```

---

## 十五、安全加固

### 15.1 系统更新策略

```bash
# 启用自动安全更新
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades

# 手动配置
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
# Unattended-Upgrade::Allowed-Origins {
#     "${distro_id}:${distro_codename}-security";
# };

# 配置自动更新计划
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
# APT::Periodic::Update-Package-Lists "1";
# APT::Periodic::Unattended-Upgrade "1";
```

### 15.2 SSH 安全加固

```bash
sudo nano /etc/ssh/sshd_config

# 安全配置
Port 2222                       # 非默认端口
PermitRootLogin no              # 禁止 root 登录
PasswordAuthentication no       # 仅密钥登录
PubkeyAuthentication yes        # 允许密钥
MaxAuthTries 3                  # 最大尝试次数
LoginGraceTime 30               # 登录超时
ClientAliveInterval 300         # 心跳检测
ClientAliveCountMax 2           # 超时断开
AllowUsers username             # 白名单用户
X11Forwarding no                # 禁用 X11
Protocol 2                      # 仅 SSH2

sudo systemctl restart ssh
```

### 15.3 防火墙配置

```bash
# UFW 基础配置
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp         # SSH（自定义端口）
sudo ufw allow 80/tcp           # HTTP
sudo ufw allow 443/tcp          # HTTPS
sudo ufw enable

# 限制 SSH 连接频率（防暴力破解）
sudo ufw limit 2222/tcp
```

### 15.4 Fail2Ban（防暴力破解）

```bash
sudo apt install -y fail2ban

# 配置
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

### 15.5 其他安全措施

```bash
# 1. 禁用不必要的服务
sudo systemctl disable bluetooth
sudo systemctl disable cups
sudo systemctl disable avahi-daemon

# 2. 文件完整性检查
sudo apt install -y aide
sudo aideinit
sudo cp /var/lib/aide/aide.db.new /var/lib/aide/aide.db
sudo aide --check

# 3. 审计日志
sudo apt install -y auditd
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
sudo ausearch -k passwd_changes

# 4. 内核安全参数
sudo nano /etc/sysctl.d/99-security.conf
# net.ipv4.ip_forward = 0
# net.ipv4.conf.all.accept_redirects = 0
# net.ipv4.conf.all.send_redirects = 0
# net.ipv4.conf.all.accept_source_route = 0
# net.ipv4.icmp_echo_ignore_broadcasts = 1
# kernel.randomize_va_space = 2
# fs.suid_dumpable = 0
sudo sysctl --system

# 5. 密码策略
sudo nano /etc/login.defs
# PASS_MAX_DAYS 90
# PASS_MIN_DAYS 7
# PASS_MIN_LEN 12
# PASS_WARN_AGE 14

# 6. 禁用 USB 存储（可选）
echo "blacklist usb-storage" | sudo tee /etc/modprobe.d/blacklist-usb.conf

# 7. 文件权限审计
sudo find / -perm -4000 -type f 2>/dev/null    # 查找 SUID 文件
sudo find / -perm -2000 -type f 2>/dev/null    # 查找 SGID 文件
```

---

## 十六、性能优化与监控

### 16.1 系统监控工具

```bash
# CPU 监控
top / htop                       # 实时监控
mpstat 1                         # CPU 统计
vmstat 1                         # 虚拟内存统计
sar -u 1                         # CPU 使用率

# 内存监控
free -h                          # 内存使用
vmstat 1                         # 内存统计
slabtop                          # 内核 slab 缓存

# 磁盘监控
iostat -xz 1                     # IO 统计
iotop                            # 按进程 IO
df -h                            # 磁盘使用
du -sh /path                     # 目录大小

# 网络监控
iftop                            # 连接流量
nethogs                          # 按进程流量
nload                            # 实时流量
ss -tunap                        # 网络连接

# 综合监控
dstat                            # 综合统计
glances                          # 系统监控（需安装）
nmon                             # 综合性能监控
```

### 16.2 性能优化

```bash
# 1. 内核参数优化（/etc/sysctl.conf）
# 网络优化
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_fin_timeout = 15
net.core.netdev_max_backlog = 65535

# 内存优化
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5

# 文件系统优化
fs.file-max = 2097152
fs.inotify.max_user_watches = 524288

sudo sysctl -p

# 2. 文件描述符限制
sudo nano /etc/security/limits.conf
# * soft nofile 65535
# * hard nofile 65535
# * soft nproc 65535
# * hard nproc 65535

# 3. I/O 调度器
cat /sys/block/sda/queue/scheduler
echo mq-deadline | sudo tee /sys/block/sda/queue/scheduler

# 4. CPU 性能模式
sudo apt install -y cpufrequtils
sudo cpufreq-set -g performance

# 5. 关闭不需要的服务
sudo systemctl list-unit-files --state=enabled
sudo systemctl disable <service_name>
```

### 16.3 日志优化

```bash
# 限制 journal 大小
sudo nano /etc/systemd/journald.conf
# SystemMaxUse=200M
# MaxRetentionSec=1month

sudo systemctl restart systemd-journald

# 清理旧日志
sudo journalctl --vacuum-size=100M
sudo journalctl --vacuum-time=7d
```

### 16.4 生成系统报告

```bash
# sysstat 工具
sudo apt install -y sysstat
sudo nano /etc/default/sysstat
# ENABLED="true"

# 查看 CPU 历史
sar -u
sar -u -f /var/log/sysstat/sa$(date +%d)

# 查看内存历史
sar -r

# 查看磁盘历史
sar -d

# 查看网络历史
sar -n DEV

# 安装并使用 dstat
sudo apt install -y dstat
dstat -tcdngm 1
```

---

## 十七、备份与恢复

### 17.1 rsync 备份

```bash
# 基本备份
rsync -avz /source/ /backup/

# 远程备份
rsync -avz /source/ user@remote:/backup/

# 远程拉取
rsync -avz user@remote:/source/ /backup/

# 常用选项
# -a  归档模式（保留属性）
# -v  显示详细信息
# -z  压缩传输
# -r  递归
# --delete     删除目标中多余的文件
# --exclude    排除文件/目录
# --progress   显示进度
# --dry-run    演练（不实际执行）

# 示例：排除不需要的文件
rsync -avz --delete \
    --exclude='.git' \
    --exclude='node_modules' \
    --exclude='*.log' \
    --exclude='__pycache__' \
    /source/ /backup/

# 增量备份（硬链接方式，节省空间）
rsync -avz --delete \
    --link-dest=/backup/previous \
    /source/ /backup/current/
```

### 17.2 Timeshift 系统快照

```bash
# 安装
sudo apt install -y timeshift

# 图形界面
timeshift-gtk

# 命令行
sudo timeshift --create --comments "安装完成"    # 创建快照
sudo timeshift --list                             # 列出快照
sudo timeshift --restore                          # 恢复
sudo timeshift --delete --snapshot '2024-01-15'  # 删除快照
```

### 17.3 备份策略建议

```
3-2-1 备份原则：
- 3 份数据副本
- 2 种不同存储介质
- 1 份异地备份

备份计划：
├── 每日：增量备份（rsync / 数据库 dump）
├── 每周：完整备份
├── 每月：归档备份（异地）
└── 每次变更前：系统快照（Timeshift）

备份内容：
├── 用户数据（/home）
├── 网站数据（/var/www）
├── 数据库（mysqldump / pg_dump）
├── 配置文件（/etc）
├── 邮件数据
└── 定时任务（crontab -l）
```

### 17.4 数据库备份

```bash
# MySQL 备份
mysqldump -u root -p database_name > backup.sql
mysqldump -u root -p --all-databases > all_backup.sql
gzip backup.sql

# MySQL 恢复
mysql -u root -p database_name < backup.sql

# PostgreSQL 备份
pg_dump -U username database_name > backup.sql
pg_dumpall -U postgres > all_backup.sql

# PostgreSQL 恢复
psql -U username database_name < backup.sql

# 自动备份脚本（配合 cron）
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/mysql"
mkdir -p "$BACKUP_DIR"
mysqldump -u root -p"$MYSQL_PASS" --all-databases | gzip > "$BACKUP_DIR/all_${DATE}.sql.gz"
find "$BACKUP_DIR" -name "*.sql.gz" -mtime +30 -delete
```

---

## 十八、故障排查

### 18.1 启动问题

```bash
# 无法进入图形界面
# 切换到 TTY：Ctrl + Alt + F2
# 登录后检查
sudo systemctl status gdm3          # 或 sddm、lightdm
sudo systemctl restart gdm3
sudo apt install --reinstall ubuntu-desktop
sudo dpkg --configure -a

# GRUB 引导修复
# 用 Live USB 启动
sudo mount /dev/sdaX /mnt
sudo mount /dev/sdaY /mnt/boot/efi  # 如果有单独 EFI 分区
sudo grub-install --root-directory=/mnt /dev/sda
sudo chroot /mnt update-grub

# 系统恢复模式
# GRUB 菜单 → Advanced options → Recovery mode
# 可以选择：clean、dpkg、fsck、grub、network、root shell
```

### 18.2 网络问题

```bash
# 网络不通
ip addr show                        # 检查 IP
ip route show                       # 检查路由
ping -c 4 8.8.8.8                   # 测试外网
ping -c 4 gateway_ip                # 测试网关

# DNS 问题
nslookup google.com                 # 测试 DNS
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# 网络服务重启
sudo systemctl restart NetworkManager
sudo netplan apply

# 网卡问题
ip link show                        # 查看网卡状态
sudo ip link set eth0 up            # 启用网卡
sudo dhclient eth0                  # 获取 IP

# 防火墙阻止
sudo ufw status                     # 检查防火墙
sudo iptables -L -n                 # 检查 iptables
```

### 18.3 磁盘问题

```bash
# 磁盘空间不足
df -h                               # 查看使用率
du -sh /var/log/*                   # 查找大文件
sudo journalctl --vacuum-size=100M  # 清理日志
sudo apt autoremove                 # 清理软件包
sudo apt clean                      # 清理缓存
docker system prune -a              # 清理 Docker

# inode 耗尽
df -i                               # 查看 inode 使用
sudo find / -xdev -type f | wc -l  # 统计文件数

# 文件系统检查
sudo fsck /dev/sda1                 # 检查修复
sudo e2fsck -f /dev/sda1            # ext4 检查

# 磁盘只读（可能磁盘故障）
dmesg | grep -i error               # 查看内核错误
sudo mount -o remount,rw /          # 重新挂载为读写
```

### 18.4 软件问题

```bash
# 依赖问题
sudo apt --fix-broken install
sudo dpkg --configure -a

# GPG 密钥错误
curl -fsSL https://archive.ubuntu.com/ubuntu/dists/$(lsb_release -cs)/Release.gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/ubuntu.gpg

# 软件包冲突
sudo apt remove conflicting_package
sudo apt install --reinstall package_name

# Snap 问题
sudo snap refresh
sudo snap changes                   # 查看变更记录
```

### 18.5 常见错误速查

| 现象 | 可能原因 | 解决方案 |
|------|----------|----------|
| `Permission denied` | 权限不足 | `sudo` 或 `chmod` |
| `Command not found` | 未安装 / PATH 问题 | `apt install` 或检查 `PATH` |
| `No space left on device` | 磁盘满 | `df -h` 检查，清理空间 |
| `Connection refused` | 服务未启动 | `systemctl start service` |
| `Could not resolve host` | DNS 问题 | 修改 `/etc/resolv.conf` |
| `dpkg: error` | 依赖损坏 | `dpkg --configure -a` |
| `Unable to locate package` | 源问题 | `apt update`，检查 sources |
| `Failed to start service` | 服务配置错误 | `journalctl -u service` |
| `Boot error` | GRUB 损坏 | Live USB 修复引导 |
| `Frozen system` | 内存/IO 问题 | `SysRq` 组合键强制重启 |

---

## 十九、Ubuntu 版本与升级

### 19.1 版本历史

| 版本 | 代号 | 发布时间 | 支持截止 | 类型 |
|------|------|----------|----------|------|
| 20.04 LTS | Focal Fossa | 2020-04 | 2025-04 | LTS |
| 22.04 LTS | Jammy Jellyfish | 2022-04 | 2027-04 | LTS |
| 23.04 | Lunar Lobster | 2023-04 | 2024-01 | 非LTS |
| 23.10 | Mantic Minotaur | 2023-10 | 2024-07 | 非LTS |
| 24.04 LTS | Noble Numbat | 2024-04 | 2029-04 | LTS |
| 24.10 | Oracular Oriole | 2024-10 | 2025-07 | 非LTS |

### 19.2 查看版本

```bash
lsb_release -a                       # 查看版本
cat /etc/os-release                  # 详细信息
hostnamectl                          # 综合信息
uname -r                             # 内核版本
uname -a                             # 完整内核信息
```

### 19.3 系统升级

```bash
# 升级当前版本的所有软件包
sudo apt update && sudo apt upgrade -y

# 发行版升级（如 22.04 → 24.04）
sudo apt install -y update-manager-core
sudo nano /etc/update-manager/release-upgrades
# Prompt=lts                       # 只提示 LTS 升级
# Prompt=normal                    # 提示所有版本升级

# 执行升级
sudo do-release-upgrade

# 升级前准备
# 1. 备份重要数据
# 2. 确保网络稳定
# 3. 关闭第三方 PPA
# 4. 确保磁盘空间充足
# 5. 建议在虚拟机中先测试
```

### 19.4 内核管理

```bash
# 查看当前内核
uname -r

# 查看已安装内核
dpkg -l | grep linux-image

# 安装新内核
sudo apt install linux-generic-hwe-24.04

# 删除旧内核
sudo apt autoremove --purge

# 查看内核启动参数
cat /proc/cmdline

# 修改启动内核
# GRUB 菜单 → Advanced options → 选择内核
sudo nano /etc/default/grub
# GRUB_DEFAULT=0                    # 默认启动项
sudo update-grub
```

---

## 二十、完整实战示例

### 20.1 部署 Web 应用

```bash
#!/bin/bash
# 部署 Web 应用脚本
set -e

APP_NAME="myapp"
APP_DIR="/opt/$APP_NAME"
NGINX_CONF="/etc/nginx/sites-available/$APP_NAME"
DOMAIN="example.com"

echo "=== 部署 $APP_NAME ==="

# 1. 安装依赖
echo "[1/6] 安装依赖..."
sudo apt update
sudo apt install -y nginx git python3 python3-pip python3-venv

# 2. 克隆代码
echo "[2/6] 克隆代码..."
sudo mkdir -p "$APP_DIR"
sudo git clone https://github.com/user/$APP_NAME.git "$APP_DIR"

# 3. 创建虚拟环境
echo "[3/6] 配置 Python 环境..."
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

    location /static/ {
        alias $APP_DIR/static/;
        expires 30d;
    }
}
EOF

sudo ln -sf "$NGINX_CONF" /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# 6. 配置防火墙
echo "[6/6] 配置防火墙..."
sudo ufw allow 'Nginx Full'
sudo ufw allow 'OpenSSH'

echo "=== 部署完成 ==="
echo "访问: http://$DOMAIN"
```

### 20.2 系统初始化脚本

```bash
#!/bin/bash
# Ubuntu 服务器初始化脚本
set -e

echo "=== Ubuntu 服务器初始化 ==="

# 更新系统
echo "[1/8] 更新系统..."
sudo apt update && sudo apt full-upgrade -y

# 设置时区
echo "[2/8] 设置时区..."
sudo timedatectl set-timezone Asia/Shanghai

# 安装基础软件
echo "[3/8] 安装基础软件..."
sudo apt install -y \
    curl wget git vim nano htop tree unzip zip \
    net-tools openssh-server fail2ban ufw \
    build-essential software-properties-common \
    apt-transport-https ca-certificates gnupg lsb-release \
    unattended-upgrades logrotate

# 配置防火墙
echo "[4/8] 配置防火墙..."
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw --force enable

# 配置 SSH 安全
echo "[5/8] 加固 SSH..."
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/#MaxAuthTries 6/MaxAuthTries 3/' /etc/ssh/sshd_config
sudo systemctl restart ssh

# 配置 Fail2Ban
echo "[6/8] 配置 Fail2Ban..."
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# 配置自动更新
echo "[7/8] 配置自动安全更新..."
sudo dpkg-reconfigure -plow unattended-upgrades

# 配置 sysctl
echo "[8/8] 优化内核参数..."
sudo tee /etc/sysctl.d/99-tuning.conf > /dev/null << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 1024 65535
vm.swappiness = 10
fs.file-max = 2097152
EOF
sudo sysctl --system

echo "=== 初始化完成 ==="
echo "建议："
echo "1. 重新登录以使用户组变更生效"
echo "2. 配置 SSH 密钥登录"
echo "3. 修改 SSH 端口（可选）"
echo "4. 重启系统"
```

### 20.3 自动化运维脚本集

**系统巡检脚本：**
```bash
#!/bin/bash
# system_check.sh - 系统巡检
REPORT="/tmp/system_check_$(date +%Y%m%d_%H%M%S).txt"

echo "=== 系统巡检报告 ===" | tee "$REPORT"
echo "生成时间: $(date)" | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 系统信息 ===" | tee -a "$REPORT"
hostnamectl | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== CPU 负载 ===" | tee -a "$REPORT"
uptime | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 内存使用 ===" | tee -a "$REPORT"
free -h | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 磁盘使用 ===" | tee -a "$REPORT"
df -h | grep -E "^/dev" | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 网络连接 ===" | tee -a "$REPORT"
ss -tlnp | head -15 | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 失败服务 ===" | tee -a "$REPORT"
systemctl list-units --state=failed --no-pager | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 最近登录 ===" | tee -a "$REPORT"
last -5 | tee -a "$REPORT"
echo "" | tee -a "$REPORT"

echo "=== 安全检查 ===" | tee -a "$REPORT"
echo "待更新软件包:" | tee -a "$REPORT"
apt list --upgradable 2>/dev/null | wc -l | tee -a "$REPORT"
echo "防火墙状态:" | tee -a "$REPORT"
sudo ufw status | tee -a "$REPORT"

echo "" | tee -a "$REPORT"
echo "报告已保存到: $REPORT"
```

**日志分析脚本：**
```bash
#!/bin/bash
# log_analysis.sh - 日志分析
LOG="${1:-/var/log/syslog}"
HOURS="${2:-24}"

echo "=== 日志分析: $LOG (最近 ${HOURS} 小时) ==="

echo ""
echo "--- 错误统计 ---"
grep -i "error" "$LOG" | tail -50 | awk '{print $1,$2,$3}' | sort | uniq -c | sort -rn | head -10

echo ""
echo "--- 警告统计 ---"
grep -i "warn" "$LOG" | tail -50 | awk '{print $1,$2,$3}' | sort | uniq -c | sort -rn | head -10

echo ""
echo "--- 登录失败 ---"
grep "Failed password" /var/log/auth.log 2>/dev/null | tail -10

echo ""
echo "--- 磁盘/IO 相关 ---"
grep -iE "disk|io|filesystem" "$LOG" | tail -10
```

---

## 附录 A：常用快捷键速查

### 终端快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl + Alt + T` | 打开终端 |
| `Ctrl + Shift + T` | 新建终端标签 |
| `Ctrl + Shift + N` | 新建终端窗口 |
| `Ctrl + Shift + C` | 复制 |
| `Ctrl + Shift + V` | 粘贴 |
| `Ctrl + C` | 终止命令 |
| `Ctrl + Z` | 暂停任务 |
| `Ctrl + D` | 退出 |
| `Ctrl + R` | 搜索历史 |
| `Ctrl + L` | 清屏 |
| `Ctrl + A / E` | 行首 / 行尾 |
| `Ctrl + U / K` | 删除行前 / 行后 |
| `Ctrl + W` | 删除前一个单词 |
| `Tab` | 自动补全 |

### 桌面快捷键

| 快捷键 | 功能 |
|--------|------|
| `Super` | 活动概览 |
| `Super + A` | 显示应用程序 |
| `Super + D` | 显示桌面 |
| `Super + L` | 锁屏 |
| `Super + ↑/↓` | 最大化 / 还原 |
| `Super + ←/→` | 半屏 |
| `Alt + Tab` | 切换窗口 |
| `Alt + F4` | 关闭窗口 |
| `Ctrl + Alt + Del` | 注销 |
| `PrtSc` | 截图 |
| `Ctrl + Alt + F1-F6` | 切换到 TTY |
| `Ctrl + Alt + F7` | 返回图形界面 |

---

## 附录 B：推荐学习资源

### 书籍

| 书名 | 作者 | 方向 |
|------|------|------|
| 《鸟哥的 Linux 私房菜》 | 鸟哥 | Linux 基础 |
| 《Linux 命令行与 Shell 脚本编程大全》 | Richard Blum | Shell 脚本 |
| 《Linux 就该这么学》 | 刘遄 | Linux 入门 |
| 《UNIX 环境高级编程》 | W. Richard Stevens | 系统编程 |
| 《Linux 性能优化实战》 | 倪朋飞 | 性能优化 |
| 《Docker 技术入门与实战》 | 杨保华 | Docker |
| 《Linux 运维之道》 | 丁明一 | 运维 |

### 在线资源

| 资源 | 网址 | 说明 |
|------|------|------|
| Ubuntu 官方文档 | https://help.ubuntu.com | 最权威 |
| Ubuntu 论坛 | https://discourse.ubuntu.com | 社区问答 |
| Ask Ubuntu | https://askubuntu.com | 问答社区 |
| Linux Journey | https://linuxjourney.com | 交互式学习 |
| Linux 命令大全 | https://www.linuxcool.com | 命令速查 |
| Arch Wiki | https://wiki.archlinux.org | 通用 Linux 知识 |
| DigitalOcean 教程 | https://www.digitalocean.com/community/tutorials | 实用教程 |
| Linux 基础 | https://linuxtools-rst.readthedocs.io | 工具详解 |

---

> **总结：** Ubuntu 是一个功能强大、社区活跃的 Linux 发行版，从桌面到服务器、从开发到运维都能胜任。掌握命令行是精通 Ubuntu 的关键，多动手实践、多阅读 man 手册、多参与社区讨论，你将快速成长为 Linux 高手。
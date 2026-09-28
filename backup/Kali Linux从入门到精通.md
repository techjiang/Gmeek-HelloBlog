> **重要声明：** Kali Linux 是专业的网络安全测试与渗透测试操作系统，仅限在**获得明确授权**的环境中使用。未经授权对任何系统进行安全测试属于违法行为。本文档旨在帮助安全从业者、学生和研究人员正确学习和使用 Kali Linux。

---

## 目录

- [一、Kali Linux 简介](#一kali-linux-简介)
- [二、系统安装](#二系统安装)
- [三、初始配置](#三初始配置)
- [四、基础操作](#四基础操作)
- [五、软件包管理](#五软件包管理)
- [六、工具体系概览](#六工具体系概览)
- [七、网络基础与配置](#七网络基础与配置)
- [八、Web 应用安全测试基础](#八web-应用安全测试基础)
- [九、无线安全测试基础](#九无线安全测试基础)
- [十、数字取证与逆向基础](#十数字取证与逆向基础)
- [十一、脚本与自动化](#十一脚本与自动化)
- [十二、虚拟化与实验室搭建](#十二虚拟化与实验室搭建)
- [十三、学习路径与认证](#十三学习路径与认证)
- [十四、法律与伦理规范](#十四法律与伦理规范)
- [十五、常见问题排查](#十五常见问题排查)

---

## 一、Kali Linux 简介

### 什么是 Kali Linux

Kali Linux 是由 **Offensive Security** 开发的基于 Debian 的 Linux 发行版，专门用于：

- 渗透测试（Penetration Testing）
- 安全审计（Security Auditing）
- 数字取证（Digital Forensics）
- 逆向工程（Reverse Engineering）
- 安全研究与教育

### 主要特点

| 特点 | 说明 |
|------|------|
| 预装 600+ 安全工具 | 涵盖信息收集、漏洞分析、Web 测试、密码攻击等 |
| 基于 Debian | 稳定、软件生态丰富 |
| 免费开源 | 完全免费，源码开放 |
| 多架构支持 | x86_64、ARM、虚拟机镜像、云镜像 |
| 可定制性 | 支持高度定制与自定义 ISO |
| 滚动更新 | 持续获取最新工具与安全更新 |

### 适用人群

- 网络安全从业者 / 渗透测试工程师
- 安全运维人员
- 网络安全专业学生
- CTF 竞赛选手
- 安全研究人员

### 与其他发行版对比

| 发行版 | 定位 | 特点 |
|--------|------|------|
| **Kali Linux** | 渗透测试 | 工具最全，社区最大 |
| **Parrot Security** | 渗透 + 隐私 | 更轻量，注重隐私保护 |
| **BlackArch** | 渗透测试 | 基于 Arch，工具超 2800 个 |
| **BackBox** | 安全测试 | 基于 Ubuntu，轻量 |
| **REMnux** | 恶意软件分析 | 专注逆向与恶意代码分析 |

### 官方资源

- 官网：https://www.kali.org
- 文档：https://www.kali.org/docs
- 论坛：https://forums.kali.org
- 博客：https://www.kali.org/blog
- GitHub：https://gitlab.com/kalilinux

---

## 二、系统安装

### 2.1 下载镜像

从官网下载页面获取最新镜像：https://www.kali.org/get-kali

**常见镜像类型：**

| 镜像类型 | 适用场景 | 大小 |
|----------|----------|------|
| Installer | 完整安装到物理机 | ~4GB |
| Weekly | 每周构建的安装镜像 | ~4GB |
| Virtual Machines | 预配置虚拟机镜像 | ~3-5GB |
| VMware / VirtualBox | 专用虚拟机镜像 | ~3-5GB |
| ARM | 树莓派等 ARM 设备 | 变化 |
| Cloud | AWS / Azure / Docker | 变化 |
| NetInstaller | 网络安装（最小镜像） | ~400MB |

**验证镜像完整性（重要）：**
```bash
# 下载 SHA256SUMS 文件后验证
sha256sum -c SHA256SUMS 2>&1 | grep -v 'FAILED' | grep OK

# 或单独计算
sha256sum kali-linux-2024.x-installer-amd64.iso
```

### 2.2 虚拟机安装（推荐入门）

#### VMware Workstation

```
1. 打开 VMware → 文件 → 新建虚拟机
2. 选择"典型"配置
3. 选择"稍后安装操作系统"
4. 客户机操作系统选择 Linux → Debian 12.x 64-bit
5. 命名虚拟机，选择存储位置
6. 磁盘大小建议 ≥ 80GB（勾选"将虚拟磁盘拆分成多个文件"）
7. 自定义硬件：
   - 内存：≥ 4GB（推荐 8GB）
   - 处理器：≥ 2 核（推荐 4 核）
   - 网络适配器：
     - NAT（可以上网，推荐入门）
     - 桥接（与主机同一网络段）
     - 仅主机（隔离环境，推荐安全测试）
8. 选择 ISO 镜像文件
9. 开启虚拟机进行安装
```

#### VirtualBox

```
1. 打开 VirtualBox → 新建
2. 名称：Kali Linux
3. 类型：Linux
4. 版本：Debian (64-bit)
5. 内存：≥ 4096MB
6. 创建虚拟硬盘：
   - 类型：VDI
   - 大小：≥ 80GB
7. 设置 → 存储 → 选择 ISO 镜像
8. 设置 → 网络 → 选择网络模式
9. 启动进行安装
```

### 2.3 物理机安装

**准备工作：**
- 8GB 以上 U 盘
- 制作启动盘工具（Rufus / balenaEtcher / dd）
- 备份重要数据

**制作启动盘：**
```bash
# Linux 下使用 dd
lsblk                          # 确认 U 盘设备名（如 /dev/sdb）
sudo dd if=kali-linux-2024.x-installer-amd64.iso of=/dev/sdb bs=4M status=progress
sync
```

```
# Windows 下使用 Rufus
1. 打开 Rufus
2. 选择 U 盘设备
3. 选择 Kali ISO 镜像
4. 分区类型：GPT（UEFI）或 MBR（Legacy BIOS）
5. 文件系统：FAT32
6. 点击"开始"
```

**安装步骤：**
```
1. 设置 BIOS/UEFI 从 U 盘启动
2. 选择安装方式：
   - Graphical Install（图形安装，推荐）
   - Install（命令行安装）
   - Live（试用，不安装）
   - Advanced options
3. 语言：中文（简体）或 English
4. 地区：China 或你的所在地区
5. 键盘布局：American English（推荐）
6. 主机名：kali（自定义）
7. 域名：留空
8. 设置 root 密码（牢记）
9. 分区方案：
   - Guided - use entire disk（整个磁盘，推荐）
   - Guided - use entire disk and set up LVM（LVM 分区）
   - Guided - use entire disk and set up encrypted LVM（加密 LVM）
   - Manual（手动分区）
10. 软件选择：
    - 桌面环境：Xfce（推荐）/ GNOME / KDE
    - 工具集合：根据需要勾选
11. 安装 GRUB 引导
12. 安装完成，重启
```

**推荐分区方案（手动）：**
```
/         50-60GB   ext4    系统根分区
/boot     1GB       ext4    引导分区（可选）
/swap     内存大小   swap    交换分区（内存 < 8GB 时建议）
/home     剩余空间   ext4    用户数据分区
```

### 2.4 双系统安装（Windows + Kali）

```
1. 在 Windows 中压缩磁盘，留出 ≥ 80GB 未分配空间
2. 设置 BIOS/UEFI：
   - 关闭 Secure Boot
   - 关闭 Fast Boot
   - 设置从 U 盘启动
3. 安装 Kali，分区时选择"手动"
4. 在未分配空间中创建分区（不要动 Windows 分区）
5. 安装 GRUB（会自动检测 Windows）
6. 重启后可选择进入 Windows 或 Kali
```

### 2.5 WSL2 安装（Windows 用户推荐）

```powershell
# 启用 WSL
wsl --install

# 或者手动启用
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# 重启后设置 WSL2 为默认
wsl --set-default-version 2

# 从 Microsoft Store 安装 Kali Linux
# 或命令行：
wsl --install -d kali-linux

# 更新 Kali
sudo apt update && sudo apt full-upgrade -y
```

> ⚠️ WSL2 版本的 Kali 部分功能受限（如无线网卡注入、某些需要内核模块的工具）。

### 2.6 Docker 安装

```bash
# 拉取 Kali Docker 镜像
docker pull kalilinux/kali-rolling

# 运行容器
docker run -it --rm kalilinux/kali-rolling /bin/bash

# 在容器内更新并安装工具
apt update && apt install -y kali-linux-headless

# 运行指定工具
docker run -it --rm kalilinux/kali-rolling nmap --version
```

---

## 三、初始配置

### 3.1 系统更新

```bash
# 更新软件包索引
sudo apt update

# 升级所有软件包
sudo apt full-upgrade -y

# 清理不需要的包
sudo apt autoremove -y
sudo apt autoclean

# 重新启动（如果内核更新了）
sudo reboot
```

**配置自动更新：**
```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
```

### 3.2 创建普通用户

```bash
# 默认只有 root 用户，建议创建普通用户
sudo adduser kaliuser

# 添加到 sudo 组
sudo usermod -aG sudo kaliuser

# 切换用户
su - kaliuser

# 测试 sudo
sudo whoami
```

**配置 sudo 免密码（可选）：**
```bash
sudo visudo
# 添加一行：
kaliuser ALL=(ALL:ALL) NOPASSWD: ALL
```

### 3.3 中文支持

```bash
# 安装中文字体
sudo apt install -y fonts-wqy-zenhei fonts-wqy-microhei

# 安装中文输入法（Fcitx5）
sudo apt install -y fcitx5 fcitx5-chinese-addons

# 设置环境变量
echo 'export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx' >> ~/.profile

# 设置系统语言
sudo dpkg-reconfigure locales
# 选择 zh_CN.UTF-8 UTF-8
```

### 3.4 网络配置

**有线网络（DHCP）：**
```bash
# NetworkManager（图形界面管理）
nmcli device status
nmcli device connect eth0

# 或使用 dhclient
sudo dhclient eth0
```

**静态 IP 配置：**
```bash
# 编辑 /etc/network/interfaces
sudo nano /etc/network/interfaces

# 添加配置
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4

# 重启网络
sudo systemctl restart networking
```

**NetworkManager 命令行配置：**
```bash
# 查看连接
nmcli connection show

# 修改连接
nmcli connection modify "Wired connection 1" ipv4.addresses 192.168.1.100/24
nmcli connection modify "Wired connection 1" ipv4.gateway 192.168.1.1
nmcli connection modify "Wired connection 1" ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection modify "Wired connection 1" ipv4.method manual

# 生效
nmcli connection up "Wired connection 1"
```

**DNS 配置：**
```bash
# 编辑 /etc/resolv.conf（临时）
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# 永久配置（systemd-resolved）
sudo nano /etc/systemd/resolved.conf
# DNS=8.8.8.8 8.8.4.4
sudo systemctl restart systemd-resolved
```

### 3.5 SSH 配置

```bash
# 安装 SSH 服务
sudo apt install -y openssh-server

# 启动服务
sudo systemctl enable ssh
sudo systemctl start ssh

# 查看状态
sudo systemctl status ssh

# 查看端口
ss -tlnp | grep ssh

# 远程连接
ssh kaliuser@192.168.1.100

# 修改 SSH 配置（/etc/ssh/sshd_config）
sudo nano /etc/ssh/sshd_config
# 常用配置：
# Port 2222                     # 修改端口
# PermitRootLogin no            # 禁止 root 登录
# PasswordAuthentication yes    # 允许密码登录
# PubkeyAuthentication yes      # 允许密钥登录

# 重启 SSH
sudo systemctl restart ssh
```

**SSH 密钥登录（更安全）：**
```bash
# 生成密钥对
ssh-keygen -t ed25519 -C "kali@lab"

# 复制公钥到远程主机
ssh-copy-id kaliuser@192.168.1.100

# 使用密钥登录
ssh -i ~/.ssh/id_ed25519 kaliuser@192.168.1.100
```

### 3.6 桌面环境配置

**安装额外桌面（以 KDE 为例）：**
```bash
sudo apt install -y kali-desktop-kde

# 切换默认桌面
sudo update-alternatives --config x-session-manager

# 或在登录界面选择桌面环境
```

**常用桌面工具：**
```bash
sudo apt install -y \
    firefox-esr \
    flameshot \
    terminator \
    vlc \
    gparted \
    timeshift
```

### 3.7 主题与外观

```bash
# 安装 Kali 主题
sudo apt install -y kali-themes-common

# Xfce 外观设置
# 应用程序 → 设置 → 外观 → 选择 Kali-Dark 主题

# 终端美化
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 3.8 系统快照与备份

```bash
# Timeshift 系统快照（推荐）
sudo apt install -y timeshift

# 命令行创建快照
sudo timeshift --create --comments "安装完成后"

# 列出快照
sudo timeshift --list

# 恢复快照
sudo timeshift --restore

# rsync 备份
sudo rsync -aAXv --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found"} / /mnt/backup/
```

---

## 四、基础操作

### 4.1 终端基础

```bash
# 打开终端快捷键
# Xfce: Ctrl + Alt + T
# GNOME: Ctrl + Alt + T

# 常用快捷键
Ctrl + C          # 终止当前命令
Ctrl + Z          # 暂停任务（fg 恢复）
Ctrl + D          # 退出终端 / 输入结束
Ctrl + R          # 搜索历史命令
Ctrl + L          # 清屏
Ctrl + A          # 光标移到行首
Ctrl + E          # 光标移到行尾
Ctrl + U          # 删除光标前内容
Ctrl + K          # 删除光标后内容
Ctrl + W          # 删除前一个单词
Tab               # 自动补全
```

### 4.2 文件系统结构

```
/                 根目录
├── bin           基本命令（二进制文件）
├── boot          引导文件
├── dev           设备文件
├── etc           系统配置文件
├── home          用户主目录
│   ├── kali      默认用户目录
│   └── kaliuser  自建用户目录
├── lib / lib64   系统库文件
├── media         可移动设备挂载点
├── mnt           临时挂载点
├── opt           第三方软件
├── proc          进程信息（虚拟文件系统）
├── root          root 用户主目录
├── run           运行时数据
├── sbin          系统管理命令
├── srv           服务数据
├── sys           系统信息（虚拟文件系统）
├── tmp           临时文件
├── usr           用户程序和数据
│   ├── bin       用户命令
│   ├── local     本地安装的软件
│   ├── share     共享数据
│   └── sbin      系统管理命令
└── var           可变数据（日志等）
    ├── log       日志文件
    └── www       Web 服务器数据
```

### 4.3 Kali 常用目录

```bash
# 工具相关
/usr/share/wordlists/       # 字典文件
/usr/share/nmap/            # Nmap 脚本
/usr/share/metasploit-framework/  # Metasploit
/usr/share/john/            # John the Ripper
/usr/share/hashcat/         # Hashcat
/usr/share/dirb/            # Dirb 字典
/usr/share/wordlists/rockyou.txt.gz  # 常用密码字典

# 日志
/var/log/auth.log           # 认证日志
/var/log/syslog             # 系统日志
/var/log/kern.log           # 内核日志

# 网络配置
/etc/network/interfaces     # 网络接口配置
/etc/hosts                  # 主机名映射
/etc/resolv.conf            # DNS 配置
/etc/ssh/sshd_config        # SSH 服务配置
```

### 4.4 用户与权限

```bash
# 用户管理
sudo adduser username           # 创建用户
sudo userdel -r username        # 删除用户
sudo usermod -aG sudo username  # 添加到 sudo 组
sudo passwd username            # 修改密码
groups username                 # 查看用户所属组
id username                     # 查看用户 UID/GID

# 文件权限
chmod 755 file                  # rwxr-xr-x
chmod +x script.sh              # 添加执行权限
chmod -R 755 directory/         # 递归修改
chown user:group file           # 修改所有者
chown -R user:group directory/

# 特殊权限
chmod +s file                   # SUID（以文件所有者身份执行）
chmod +t directory              # Sticky bit（只有所有者能删除文件）
```

### 4.5 进程管理

```bash
# 查看进程
ps aux                          # 所有进程
ps aux | grep nmap              # 查找进程
top                             # 实时监控
htop                            # 更好的监控工具
pgrep -f "process_name"         # 按名称查找 PID

# 管理进程
kill PID                        # 终止进程
kill -9 PID                     # 强制终止
killall process_name            # 按名称终止
pkill -f "pattern"              # 按模式终止

# 后台运行
nohup command &                 # 后台运行，不受终端关闭影响
screen -S session_name          # 创建 screen 会话
tmux new -s session_name        # 创建 tmux 会话

# systemd 服务管理
systemctl start/stop/restart/status/enable/disable service_name
systemctl list-units --type=service
journalctl -u service_name -f   # 查看服务日志
```

---

## 五、软件包管理

### 5.1 APT 包管理

```bash
# 基本操作
sudo apt update                          # 更新软件包索引
sudo apt upgrade                         # 升级已安装包
sudo apt full-upgrade                    # 完整升级（处理依赖变化）
sudo apt install package_name            # 安装
sudo apt install -y package_name         # 自动确认安装
sudo apt remove package_name             # 卸载（保留配置）
sudo apt purge package_name              # 彻底卸载
sudo apt autoremove                      # 清理不需要的依赖
sudo apt search keyword                  # 搜索
sudo apt show package_name               # 查看包详情
sudo apt list --installed                # 已安装列表
sudo apt list --upgradable               # 可升级列表
```

### 5.2 Kali 元包（工具集合）

```bash
# 核心工具集
sudo apt install kali-linux-core          # 核心工具
sudo apt install kali-linux-default       # 默认工具集
sudo apt install kali-linux-large         # 大型工具集
sudo apt install kali-linux-everything    # 所有工具（非常大）

# 按类别安装
sudo apt install kali-tools-web           # Web 应用安全
sudo apt install kali-tools-passwords     # 密码攻击
sudo apt install kali-tools-wireless      # 无线安全
sudo apt install kali-tools-reverse-engineering  # 逆向工程
sudo apt install kali-tools-forensics     # 数字取证
sudo apt install kali-tools-exploitation   # 漏洞利用
sudo apt install kali-tools-sniffing-spoofing    # 嗅探与欺骗
sudo apt install kali-tools-vulnerability # 漏洞分析
sudo apt install kali-tools-information-gathering # 信息收集
sudo apt install kali-tools-post-exploitation     # 后渗透
sudo apt install kali-tools-social-engineering    # 社会工程

# 查看所有元包
apt list 2>/dev/null | grep kali-tools-
```

### 5.3 安装第三方软件

```bash
# 安装 .deb 包
sudo dpkg -i package.deb
sudo apt install -f                     # 修复依赖

# 安装 .tar.gz 源码包
tar -xzf package.tar.gz
cd package
./configure
make
sudo make install

# Snap 包
sudo apt install snapd
sudo snap install package_name

# Flatpak
sudo apt install flatpak
flatpak install flathub package_name

# Python 包
pip3 install package_name
pip3 install -r requirements.txt

# Node.js 包
npm install -g package_name

# Go 工具
go install github.com/user/tool@latest
```

### 5.4 添加仓库

```bash
# 编辑源列表
sudo nano /etc/apt/sources.list

# Kali 官方源（国内可换阿里云/清华源）
# 官方源
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware

# 清华源
deb https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free non-free-firmware

# 阿里云源
deb https://mirrors.aliyun.com/kali kali-rolling main contrib non-free non-free-firmware

# 更新
sudo apt update
```

---

## 六、工具体系概览

> 以下为 Kali 预装工具的**分类概览**，帮助了解工具生态。具体使用请在授权环境中学习。

### 6.1 信息收集（Information Gathering）

| 工具 | 用途 |
|------|------|
| `nmap` | 网络扫描与主机发现 |
| `masscan` | 大规模端口扫描 |
| `dnsenum` | DNS 信息收集 |
| `dnsrecon` | DNS 枚举 |
| `theHarvester` | 邮箱、子域名收集 |
| `recon-ng` | 信息收集框架 |
| `Maltego` | 开源情报分析（图形化） |
| `spiderfoot` | 自动化 OSINT |
| `shodan` | 网络设备搜索引擎 |
| `whois` | 域名注册信息查询 |

### 6.2 漏洞分析（Vulnerability Analysis）

| 工具 | 用途 |
|------|------|
| `nikto` | Web 服务器漏洞扫描 |
| `nuclei` | 模板化漏洞扫描 |
| `openvas` | 综合漏洞评估系统 |
| `lynis` | 系统安全审计 |
| `legion` | 网络漏洞扫描（图形化） |

### 6.3 Web 应用安全（Web Application Analysis）

| 工具 | 用途 |
|------|------|
| `burpsuite` | Web 安全测试平台 |
| `sqlmap` | SQL 注入检测与利用 |
| `dirb` / `dirbuster` | 目录/文件枚举 |
| `gobuster` | 目录/文件/DNS 枚举 |
| `ffuf` | Web Fuzzer |
| `wpscan` | WordPress 安全扫描 |
| `whatweb` | Web 技术识别 |
| `commix` | 命令注入测试 |
| `zaproxy` | OWASP ZAP 代理 |

### 6.4 密码攻击（Password Attacks）

| 工具 | 用途 |
|------|------|
| `hashcat` | GPU 密码破解 |
| `john` | John the Ripper 密码破解 |
| `hydra` | 网络登录暴力破解 |
| `medusa` | 并行网络登录破解 |
| `cewl` | 从网站生成字典 |
| `crunch` | 自定义字典生成 |
| `wordlists` | 内置字典集合 |

### 6.5 无线安全（Wireless Attacks）

| 工具 | 用途 |
|------|------|
| `aircrack-ng` | 无线安全审计套件 |
| `wifite` | 自动化无线攻击 |
| `kismet` | 无线网络检测 |
| `fern-wifi-cracker` | 图形化 WiFi 审计 |
| `hostapd-mana` | 恶意热点创建 |
| `macchanger` | MAC 地址修改 |

### 6.6 漏洞利用（Exploitation Tools）

| 工具 | 用途 |
|------|------|
| `metasploit-framework` | 渗透测试框架 |
| `msfconsole` | Metasploit 控制台 |
| `searchsploit` | Exploit-DB 离线搜索 |
| `beef-xss` | 浏览器利用框架 |
| `commix` | 命令注入利用 |
| `sqlmap` | SQL 注入利用 |

### 6.7 嗅探与欺骗（Sniffing & Spoofing）

| 工具 | 用途 |
|------|------|
| `wireshark` | 网络抓包分析（图形化） |
| `tcpdump` | 命令行抓包 |
| `ettercap` | 中间人攻击 |
| `bettercap` | 网络攻击与监控 |
| `responder` | LLMNR/NBT-NS 中毒 |
| `mitmproxy` | HTTP/HTTPS 代理 |

### 6.8 后渗透（Post Exploitation）

| 工具 | 用途 |
|------|------|
| `meterpreter` | Metasploit 后渗透载荷 |
| `empire` | 后渗透框架 |
| `powersploit` | PowerShell 后渗透工具 |
| `weevely` | PHP Webshell 管理 |
| `chisel` | 隧道工具 |
| `proxychains` | 代理链 |

### 6.9 数字取证（Forensics）

| 工具 | 用途 |
|------|------|
| `autopsy` | 数字取证平台（图形化） |
| `sleuthkit` | 文件系统分析 |
| `volatility` | 内存取证 |
| `binwalk` | 固件分析 |
| `foremost` | 文件恢复 |
| `bulk_extractor` | 大规模数据提取 |
| `dc3dd` | 取证级磁盘拷贝 |

### 6.10 逆向工程（Reverse Engineering）

| 工具 | 用途 |
|------|------|
| `ghidra` | NSA 开源逆向工具 |
| `radare2` | 逆向分析框架 |
| `gdb` | GNU 调试器 |
| `pwntools` | CTF/PWN 开发框架 |
| `checksec` | 二进制安全检查 |
| `strace` | 系统调用追踪 |
| `ltrace` | 库函数调用追踪 |

### 6.11 社会工程（Social Engineering）

| 工具 | 用途 |
|------|------|
| `set` (SET) | 社会工程学工具集 |
| `maltego` | 情报分析 |
| `theHarvester` | 信息收集 |

### 6.12 报告工具

| 工具 | 用途 |
|------|------|
| `dradis` | 渗透测试报告协作 |
| `faraday` | 集成安全测试平台 |
| `pandoc` | 文档格式转换 |
| `cutycapt` | 网页截图 |
| `keepnote` | 笔记工具 |

---

## 七、网络基础与配置

### 7.1 网络模式

```
NAT（网络地址转换）：
- 虚拟机通过主机共享 IP 上网
- 外部无法直接访问虚拟机
- 适合：上网、下载工具、一般学习

桥接（Bridged）：
- 虚拟机与主机在同一网络，独立 IP
- 同网络其他设备可以访问
- 适合：模拟真实网络环境

仅主机（Host-Only）：
- 虚拟机只能与主机通信
- 完全隔离的网络环境
- 适合：安全测试、靶场实验

内部网络（Internal）：
- 虚拟机之间可以通信
- 与主机和外部网络隔离
- 适合：多虚拟机互联实验
```

### 7.2 网络排查

```bash
# 查看网络接口
ip addr show
ifconfig

# 查看路由表
ip route show
route -n

# 测试连通性
ping -c 4 8.8.8.8
ping -c 4 google.com

# DNS 解析
nslookup google.com
dig google.com
dig +short google.com

# 查看连接
ss -tlnp
netstat -tlnp

# 追踪路由
traceroute google.com
mtr google.com

# 抓包
sudo tcpdump -i eth0 -nn
sudo tcpdump -i eth0 port 80 -nn
sudo tcpdump -i any -w capture.pcap

# 查看 ARP 表
arp -a
ip neigh show

# 接口监控
nload                           # 实时流量监控
iftop                           # 连接流量监控
nethogs                         # 按进程显示流量
```

### 7.3 防火墙（iptables / nftables）

```bash
# iptables 基本操作
sudo iptables -L -n -v                  # 查看规则
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT   # 允许 SSH
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT   # 允许 HTTP
sudo iptables -A INPUT -j DROP                       # 拒绝其他
sudo iptables -F                                   # 清除规则

# 保存规则
sudo apt install iptables-persistent
sudo netfilter-persistent save

# nftables（新版默认）
sudo nft list ruleset
sudo nft add table inet filter
sudo nft add chain inet filter input { type filter hook input priority 0 \; }
```

### 7.4 代理配置

```bash
# 环境变量代理
export http_proxy=http://127.0.0.1:8080
export https_proxy=http://127.0.0.1:8080
export no_proxy=localhost,127.0.0.1

# apt 代理
sudo nano /etc/apt/apt.conf.d/proxy
# Acquire::http::Proxy "http://127.0.0.1:8080";

# proxychains（通过代理运行工具）
sudo nano /etc/proxychains4.conf
# 在末尾添加 SOCKS5 代理
# socks5 127.0.0.1 9050

proxychains nmap -sT target_ip
```

### 7.5 VPN 配置

```bash
# OpenVPN
sudo apt install -y openvpn
sudo openvpn --config client.ovpn

# WireGuard
sudo apt install -y wireguard
sudo wg-quick up wg0

# 查看 VPN 状态
sudo wg show
```

---

## 八、Web 应用安全测试基础

### 8.1 测试流程概览

```
合法 Web 安全测试的标准流程：

1. 信息收集
   ├── 域名 / 子域名枚举
   ├── IP / 端口识别
   ├── Web 技术指纹识别
   ├── 目录 / 文件枚举
   └── 搜索引擎 / GitHub 信息泄露

2. 漏洞扫描
   ├── 自动化扫描（Nikto / Nuclei / OWASP ZAP）
   ├── 手动测试（Burp Suite）
   └── 版本漏洞匹配

3. 漏洞验证与利用（在授权范围内）
   ├── SQL 注入测试
   ├── XSS 测试
   ├── 文件上传测试
   ├── 认证 / 会话管理测试
   ├── 访问控制测试
   └── 业务逻辑测试

4. 报告输出
   ├── 漏洞描述
   ├── 复现步骤
   ├── 风险等级
   ├── 修复建议
   └── 截图 / 证据
```

### 8.2 OWASP Top 10（2021）

```
A01: Broken Access Control          失效的访问控制
A02: Cryptographic Failures         加密机制失效
A03: Injection                      注入（SQL、NoSQL、OS 命令、LDAP）
A04: Insecure Design                不安全的设计
A05: Security Misconfiguration      安全配置错误
A06: Vulnerable Components          脆弱的组件
A07: Identification & Auth Failures 身份认证与会话管理失效
A08: Software & Data Integrity      软件与数据完整性失效
A09: Security Logging Failures      安全日志与监控失效
A10: SSRF                           服务端请求伪造
```

### 8.3 常用 Web 测试工具操作

```bash
# 目录枚举
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt -x php,html,txt
ffuf -u http://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt

# 技术识别
whatweb http://target.com
nikto -h http://target.com

# Nuclei 漏洞扫描
nuclei -u http://target.com -t /path/to/templates/
nuclei -l urls.txt -severity high,critical

# WordPress 扫描
wpscan --url http://target.com --enumerate u,ap,at

# 目录扫描字典
ls /usr/share/wordlists/dirb/
ls /usr/share/seclists/    # 需安装
sudo apt install seclists
```

### 8.4 Burp Suite 使用要点

```
1. 配置代理
   - Burp → Proxy → Options → 监听 127.0.0.1:8080
   - 浏览器设置代理：127.0.0.1:8080
   - 导入 Burp CA 证书（拦截 HTTPS 需要）

2. 核心模块
   - Proxy：抓包与修改请求
   - Repeater：手动重放请求
   - Intruder：自动化测试（参数 Fuzzing）
   - Scanner：自动漏洞扫描（Pro 版）
   - Decoder：编码/解码工具
   - Comparer：对比工具
   - Extender：插件扩展

3. 常用操作
   - 右键 → Send to Repeater（手动测试）
   - 右键 → Send to Intruder（参数测试）
   - 右键 → Do active scan（主动扫描）
```

### 8.5 SQL 注入测试（基础概念）

```sql
-- SQL 注入检测（在授权测试环境中）
-- 输入 ' 观察是否报错
-- 输入 ' OR '1'='1 观察是否返回全部数据
-- 输入 ' AND 1=1 -- 与 ' AND 1=2 -- 对比差异

-- sqlmap 自动化检测（授权测试）
sqlmap -u "http://target.com/page?id=1" --dbs
sqlmap -u "http://target.com/page?id=1" --tables -D database_name
sqlmap -u "http://target.com/page?id=1" --dump -T table_name -D database_name

-- 从请求文件运行
sqlmap -r request.txt --batch
```

### 8.6 安全编码实践（防御视角）

```java
// 使用参数化查询（防止 SQL 注入）
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
ps.setInt(1, userId);

// 输入验证
if (!input.matches("^[a-zA-Z0-9]+$")) {
    throw new IllegalArgumentException("Invalid input");
}

// 输出编码（防止 XSS）
String safe = StringEscapeUtils.escapeHtml4(userInput);

// 安全响应头
response.setHeader("X-Content-Type-Options", "nosniff");
response.setHeader("X-Frame-Options", "DENY");
response.setHeader("Content-Security-Policy", "default-src 'self'");
response.setHeader("Strict-Transport-Security", "max-age=31536000");
```

---

## 九、无线安全测试基础

### 9.1 前提条件

```
- 支持监听模式（Monitor Mode）的无线网卡
- 常见芯片：Atheros AR9271、Ralink RT3070、Realtek RTL8812AU
- 在授权环境中测试
```

### 9.2 网卡配置

```bash
# 查看无线接口
iwconfig
ip link show

# 检查是否支持监听模式
sudo airmon-ng
sudo airmon-ng check

# 开启监听模式
sudo airmon-ng start wlan0

# 查看监听接口
iwconfig wlan0mon

# 关闭监听模式
sudo airmon-ng stop wlan0mon

# 修改 MAC 地址
sudo macchanger -r wlan0
```

### 9.3 无线安全评估流程（授权测试）

```
1. 侦察（Reconnaissance）
   - 识别目标 AP（SSID、BSSID、信道、加密方式）
   - 识别客户端设备

2. 抓包（Capture）
   - 捕获握手包（WPA/WPA2）
   - 捕获管理帧

3. 分析（Analysis）
   - 分析加密强度
   - 识别配置弱点

4. 报告（Report）
   - 记录发现
   - 提供加固建议
```

### 9.4 无线安全加固建议

```
1. 使用 WPA3 或 WPA2-AES（避免 WEP、WPA-TKIP）
2. 使用强密码（12 位以上，混合字符）
3. 隐藏 SSID（辅助措施）
4. 启用 MAC 过滤（辅助措施）
5. 关闭 WPS
6. 更新路由器固件
7. 定期更换密码
8. 使用企业级认证（802.1X / RADIUS）
9. 网络隔离（访客网络、IoT 网络分开）
10. 启用无线入侵检测（WIDS）
```

---

## 十、数字取证与逆向基础

### 10.1 数字取证概述

```
数字取证主要方向：
├── 磁盘取证：磁盘镜像、文件恢复、时间线分析
├── 内存取证：内存转储、进程分析、恶意代码检测
├── 网络取证：流量分析、协议还原、攻击痕迹追踪
└── 移动设备取证：手机数据提取与分析
```

### 10.2 磁盘取证

```bash
# 创建磁盘镜像（取证级）
sudo dc3dd if=/dev/sdb of=/evidence/disk.img hash=sha256 log=/evidence/log.txt

# 或使用 dd
sudo dd if=/dev/sdb of=/evidence/disk.img bs=4M status=progress

# 验证镜像完整性
sha256sum /evidence/disk.img

# 挂载镜像（只读）
sudo mount -o ro,loop /evidence/disk.img /mnt/evidence

# 文件系统分析（Sleuth Kit）
fls /evidence/disk.img              # 列出文件
icat /evidence/disk.img 48          # 提取文件（按 inode）
mmls /evidence/disk.img             # 查看分区表
fsstat /evidence/disk.img           # 文件系统信息
mactime -d /evidence/timeline.body  # 时间线分析

# 文件恢复
foremost -i /evidence/disk.img -o /evidence/recovered/
photorec /evidence/disk.img

# Autopsy（图形化取证平台）
sudo autopsy
# 浏览器访问 http://localhost:9999/autopsy
```

### 10.3 内存取证

```bash
# 获取内存转储（Windows - WinPmem）
# Linux - LiME 或 /proc/kcore

# Volatility 内存分析
volatility -f memory.dump imageinfo                    # 识别系统信息
volatility -f memory.dump --profile=Win10x64 pslist     # 进程列表
volatility -f memory.dump --profile=Win10x64 netscan   # 网络连接
volatility -f memory.dump --profile=Win10x64 filescan  # 文件扫描
volatility -f memory.dump --profile=Win10x64 hashdump  # 密码哈希
volatility -f memory.dump --profile=Win10x64 malfind   # 可疑代码
```

### 10.4 逆向工程基础

```bash
# 文件分析
file binary                     # 识别文件类型
strings binary | grep -i flag   # 提取字符串
binwalk binary                  # 分析嵌入文件
checksec binary                 # 检查安全机制

# 静态分析
radare2 binary                  # Radare2 分析
# r2 常用命令：aaa（分析）、pdf（反汇编）、iz（字符串）

# Ghidra（图形化逆向）
ghidraRun

# 动态分析
gdb binary                      # 调试
strace ./binary                 # 系统调用追踪
ltrace ./binary                 # 库函数追踪

# Python 逆向辅助（pwntools）
python3 -c "from pwn import *; print(hex(ELF('./binary').entry))"
```

---

## 十一、脚本与自动化

### 11.1 Bash 脚本

```bash
#!/bin/bash
# 批量端口扫描脚本示例

TARGETS_FILE="targets.txt"
OUTPUT_DIR="scan_results"
mkdir -p "$OUTPUT_DIR"

while IFS= read -r target; do
    echo "[*] Scanning $target..."
    nmap -sV -O -oA "$OUTPUT_DIR/$target" "$target"
    echo "[+] Done: $target"
done < "$TARGETS_FILE"

echo "[*] All scans complete. Results in $OUTPUT_DIR/"
```

### 11.2 Python 脚本

```python
#!/usr/bin/env python3
"""批量端口扫描示例"""

import socket
import sys
from concurrent.futures import ThreadPoolExecutor

def scan_port(host, port):
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.settimeout(1)
            result = s.connect_ex((host, port))
            return port if result == 0 else None
    except Exception:
        return None

def scan_host(host, ports):
    open_ports = []
    with ThreadPoolExecutor(max_workers=100) as executor:
        futures = {executor.submit(scan_port, host, p): p for p in ports}
        for future in futures:
            result = future.result()
            if result:
                open_ports.append(result)
    return sorted(open_ports)

if __name__ == "__main__":
    host = sys.argv[1] if len(sys.argv) > 1 else "127.0.0.1"
    ports = range(1, 1025)
    open_ports = scan_host(host, ports)
    print(f"Open ports on {host}: {open_ports}")
```

### 11.3 Nmap 脚本引擎（NSE）

```bash
# 查看脚本
ls /usr/share/nmap/scripts/
nmap --script-help all

# 使用脚本
nmap --script=http-title -p 80 target_ip
nmap --script=vuln target_ip
nmap --script=ssl-enum-ciphers -p 443 target_ip

# 自定义脚本（Lua）
# 放在 /usr/share/nmap/scripts/ 目录
# nmap --script=my-script target_ip
```

### 11.4 自动化测试报告

```bash
# 批量扫描并生成报告
#!/bin/bash
TARGET=$1
DATE=$(date +%Y%m%d_%H%M%S)
REPORT_DIR="reports/${TARGET}_${DATE}"
mkdir -p "$REPORT_DIR"

echo "[*] Starting assessment for $TARGET"

# 端口扫描
nmap -sV -sC -oA "$REPORT_DIR/nmap" "$TARGET"

# Web 扫描
nikto -h "http://$TARGET" -output "$REPORT_DIR/nikto.txt"

# 目录枚举
gobuster dir -u "http://$TARGET" -w /usr/share/wordlists/dirb/common.txt \
    -o "$REPORT_DIR/gobuster.txt"

echo "[*] Assessment complete. Report in $REPORT_DIR/"
```

---

## 十二、虚拟化与实验室搭建

### 12.1 搭建靶场环境

```
推荐靶场平台（合法学习用）：

1. DVWA（Damn Vulnerable Web Application）
   - Web 漏洞练习
   - docker-compose up -d

2. WebGoat（OWASP）
   - OWASP Top 10 学习
   - docker run -p 8080:8080 webgoat/webgoat

3. VulnHub
   - 虚拟机靶场镜像下载
   - https://www.vulnhub.com

4. HackTheBox
   - 在线渗透测试练习平台
   - https://www.hackthebox.com

5. TryHackMe
   - 入门友好的安全学习平台
   - https://tryhackme.com

6. PortSwigger Web Security Academy
   - Web 安全免费实验室
   - https://portswigger.net/web-security

7. OWASP Juice Shop
   - 现代 Web 应用漏洞练习
   - docker run -d -p 3000:3000 bkimminich/juice-shop

8. Metasploitable
   - 故意配置脆弱的 Linux 靶机
   - 用于练习 Metasploit

9. Vulhub（Docker 漏洞环境）
   - https://github.com/vulhub/vulhub
   - 覆盖大量 CVE 漏洞环境

10. PicoCTF / CTFHub
    - CTF 竞赛练习
```

### 12.2 Docker 靶场管理

```bash
# 启动 DVWA
git clone https://github.com/digininja/DVWA.git
cd DVWA
docker-compose up -d

# 启动 Juice Shop
docker run -d --name juice-shop -p 3000:3000 bkimminich/juice-shop

# 启动 Vulhub 环境
git clone https://github.com/vulhub/vulhub.git
cd vulhub/some-vulnerability/
docker-compose up -d

# 管理
docker ps                          # 查看运行中的靶场
docker-compose down                # 停止
docker-compose logs                # 查看日志
```

### 12.3 多虚拟机实验室拓扑

```
推荐实验拓扑：

[攻击机 Kali] ←→ [内部网络 192.168.56.0/24] ←→ [靶机群]
                                                    ├── Metasploitable2
                                                    ├── DVWA (Web)
                                                    ├── OWASP Broken Web Apps
                                                    └── Windows Server (AD)

VMware/VirtualBox 网络配置：
- Kali: Host-Only + NAT（双网卡）
- 靶机: Host-Only（仅内部网络）
```

### 12.4 CTF 学习环境

```bash
# CTF 常用工具
sudo apt install -y \
    steghide \
    binwalk \
    foremost \
    zsteg \
    exiftool \
    radare2 \
    ghidra \
    gdb \
    pwntools \
    ropgadget \
    one_gadget \
    volatility3

# Python CTF 库
pip3 install pwntools pycryptodome requests beautifulsoup4 z3-solver
```

---

## 十三、学习路径与认证

### 13.1 入门阶段（0-3 个月）

```
学习内容：
├── Linux 基础命令与系统管理
├── 计算机网络基础（TCP/IP、HTTP、DNS）
├── Python / Bash 脚本基础
├── 虚拟机安装与配置
├── Kali Linux 基本操作
└── 法律法规与职业道德

推荐资源：
- Kali Linux 官方文档
- 《鸟哥的 Linux 私房菜》
- 《计算机网络：自顶向下方法》
- TryHackMe（Beginner Path）
- OverTheWire（Bandit 系列）
```

### 13.2 初级阶段（3-6 个月）

```
学习内容：
├── Web 安全基础（OWASP Top 10）
├── 网络扫描与信息收集
├── 常用安全工具（Nmap、Burp Suite、SQLMap）
├── 密码学基础
├── 操作系统安全
└── CTF 入门（Web、Misc）

推荐资源：
- PortSwigger Web Security Academy
- DVWA / WebGoat / Juice Shop
- 《白帽子讲 Web 安全》
- 《Web 应用安全权威指南》
- HackTheBox（Easy Machines）
```

### 13.3 中级阶段（6-12 个月）

```
学习内容：
├── 渗透测试方法论（PTES、OSSTMM）
├── 漏洞原理与利用
├── 内网渗透基础
├── 无线安全
├── 逆向工程入门
├── CTF 进阶（Pwn、Reverse、Crypto）
└── 安全报告编写

推荐资源：
- 《Metasploit 渗透测试指南》
- 《黑客攻防技术宝典：Web 实战篇》
- HackTheBox（Medium Machines）
- VulnHub 靶场
- CTFHub / BUUCTF / 攻防世界
```

### 13.4 高级阶段（12 个月以上）

```
学习内容：
├── 高级渗透测试（红队攻防）
├── 内网深度渗透（AD 域、横向移动）
├── 漏洞挖掘（源码审计、Fuzzing）
├── 二进制安全（Pwn、内核漏洞）
├── 安全工具开发
├── 应急响应与溯源
└── 安全架构设计

推荐资源：
- 《内网安全攻防》
- 《0day 安全：软件漏洞分析技术》
- HackTheBox（Hard/Insane Machines）
- 国内外安全会议（DEF CON、KCon、XCon）
- CVE 漏洞复现
```

### 13.5 认证体系

| 认证 | 颁发机构 | 定位 | 难度 |
|------|----------|------|------|
| **CompTIA Security+** | CompTIA | 安全基础 | ⭐⭐ |
| **CEH** | EC-Council | 道德黑客 | ⭐⭐⭐ |
| **eJPT** | INE/eLearnSecurity | 入门渗透测试 | ⭐⭐ |
| **OSCP** | OffSec (Offensive Security) | 实战渗透测试 | ⭐⭐⭐⭐ |
| **OSWE** | OffSec | Web 应用高级安全 | ⭐⭐⭐⭐⭐ |
| **OSEP** | OffSec | 高级渗透测试 | ⭐⭐⭐⭐⭐ |
| **OSCE3** | OffSec | 综合高级认证 | ⭐⭐⭐⭐⭐ |
| **GPEN** | GIAC | 渗透测试 | ⭐⭐⭐⭐ |
| **GXPN** | GIAC | 高级渗透测试 | ⭐⭐⭐⭐⭐ |
| **CISP** | 中国信息安全测评中心 | 国内安全认证 | ⭐⭐⭐ |
| **CISP-PTE** | 中国信息安全测评中心 | 渗透测试工程师 | ⭐⭐⭐⭐ |
| **CISSP** | (ISC)² | 安全管理 | ⭐⭐⭐⭐ |

### 13.6 CTF 学习资源

```
国内平台：
- BUUCTF：https://buuoj.cn
- CTFHub：https://www.ctfhub.com
- 攻防世界：https://adworld.xctf.org.cn
- XCTF 联赛：https://www.xctf.org.cn
- NCTF / HCTF / 各高校 CTF

国际平台：
- CTFtime：https://ctftime.org
- picoCTF：https://picoctf.org
- HackTheBox：https://hackthebox.com
- TryHackMe：https://tryhackme.com
- Root-Me：https://www.root-me.org
- Cryptohack：https://cryptohack.org（密码学）
```

---

## 十四、法律与伦理规范

### 14.1 相关法律法规

> 以下为中华人民共和国相关法律法规要点，请严格遵守：

```
《中华人民共和国网络安全法》
- 第 27 条：任何个人和组织不得从事非法侵入他人网络、干扰他人网络正常功能、
  窃取网络数据等危害网络安全的活动
- 第 44 条：不得窃取或者以其他非法方式获取个人信息

《中华人民共和国刑法》
- 第 285 条：非法侵入计算机信息系统罪
- 第 286 条：破坏计算机信息系统罪
- 第 287 条：利用计算机实施犯罪

《中华人民共和国数据安全法》
- 保障数据安全，促进数据开发利用

《中华人民共和国个人信息保护法》
- 保护个人信息权益，规范个人信息处理活动
```

### 14.2 合法使用原则

```
1. 授权原则
   - 任何安全测试必须获得系统所有者的书面授权
   - 授权范围必须明确（时间、目标、方法）
   - 超出授权范围的测试属于违法行为

2. 最小影响原则
   - 测试过程中尽量减少对业务的影响
   - 避免使用破坏性操作
   - 及时报告发现的问题

3. 保密原则
   - 测试中获取的数据不得泄露
   - 报告仅限授权人员阅读
   - 测试完成后清理所有测试数据

4. 职业道德
   - 不利用漏洞谋取私利
   - 不在未授权系统上练习
   - 不传播攻击工具和恶意代码
   - 尊重隐私和数据保护
```

### 14.3 合法练习环境

```
✅ 合法的学习方式：
- 在自己的虚拟机 / 靶场中练习
- 使用在线合法平台（HackTheBox、TryHackMe、DVWA）
- 参加官方授权的 CTF 竞赛
- 在公司授权范围内进行安全测试
- 参与漏洞赏金计划（Bug Bounty）
- 获得书面授权的渗透测试项目

❌ 违法的行为：
- 未经授权扫描/测试他人系统
- 窃取他人数据或密码
- 入侵他人系统或网站
- 制作/传播恶意软件
- 利用漏洞进行勒索
- 在公共 WiFi 上嗅探他人数据
```

---

## 十五、常见问题排查

### 15.1 安装问题

```bash
# 问题：安装后无法联网
# 解决：检查网络配置
ip addr show
sudo dhclient eth0
sudo systemctl restart networking

# 问题：安装后无法进入图形界面
# 解决：重装桌面环境
sudo apt install -y kali-desktop-xfce
sudo systemctl restart lightdm

# 问题：分辨率不对（虚拟机）
# 解决：安装虚拟机增强工具
# VMware: 安装 open-vm-tools
sudo apt install -y open-vm-tools open-vm-tools-desktop
sudo reboot

# VirtualBox: 安装 Guest Additions
sudo apt install -y virtualbox-guest-x11
sudo reboot

# 问题：GRUB 引导丢失（双系统）
# 解决：用 Live USB 修复
sudo mount /dev/sdaX /mnt
sudo grub-install --root-directory=/mnt /dev/sda
sudo update-grub
```

### 15.2 网络问题

```bash
# 问题：无线网卡不识别
lsusb                              # 检查 USB 设备
lsmod | grep ath                   # 检查驱动
sudo apt install -y firmware-atheros
sudo modprobe ath9k_htc

# 问题：VMware NAT 网络不通
sudo systemctl restart NetworkManager
sudo dhclient -v eth0

# 问题：DNS 解析失败
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
sudo systemctl restart systemd-resolved

# 问题：代理配置后无法上网
unset http_proxy https_proxy
```

### 15.3 软件问题

```bash
# 问题：apt update 报 GPG 错误
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys <KEY_ID>
# 或
curl -fsSL https://archive.kali.org/archive-key.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kali-archive-key.gpg

# 问题：apt 依赖损坏
sudo apt --fix-broken install
sudo dpkg --configure -a

# 问题：找不到命令
which command_name
apt search command_name
sudo apt install package_name

# 问题：Python pip 安装报错
pip3 install --break-system-packages package_name
# 或使用虚拟环境
python3 -m venv venv
source venv/bin/activate
pip install package_name
```

### 15.4 性能问题

```bash
# 问题：虚拟机运行卡顿
# 增加内存（≥ 4GB）和 CPU（≥ 2 核）
# 启用 3D 加速
# 关闭不需要的服务
sudo systemctl disable bluetooth
sudo systemctl disable cups

# 问题：磁盘空间不足
df -h                              # 查看磁盘使用
du -sh /var/log/*                  # 查看日志占用
sudo journalctl --vacuum-size=100M # 清理日志
sudo apt autoremove                # 清理软件包
docker system prune -a             # 清理 Docker

# 问题：开机慢
systemd-analyze                    # 分析启动时间
systemd-analyze blame              # 查看慢的服务
sudo systemctl disable 不需要的服务
```

---

## 附录 A：Kali 快捷键速查

| 快捷键 | 功能 |
|--------|------|
| `Ctrl + Alt + T` | 打开终端 |
| `Ctrl + Alt + F1-F6` | 切换到虚拟终端 |
| `Ctrl + Alt + F7` | 返回图形界面 |
| `Ctrl + Shift + T` | 新建终端标签 |
| `Ctrl + Shift + N` | 新建终端窗口 |
| `Ctrl + L` | 清屏 |
| `Ctrl + R` | 搜索历史命令 |
| `Ctrl + C` | 终止命令 |
| `Ctrl + Z` | 暂停任务 |
| `Ctrl + Shift + C` | 复制（终端） |
| `Ctrl + Shift + V` | 粘贴（终端） |
| `Alt + Tab` | 切换窗口 |
| `Ctrl + Alt + D` | 显示桌面 |
| `PrtSc` | 截图 |
| `Alt + F2` | 运行命令 |

## 附录 B：推荐学习资源

### 书籍

| 书名 | 作者 | 方向 |
|------|------|------|
| 《Kali Linux 渗透测试》 | OffSec | Kali 入门 |
| 《Metasploit 渲透测试指南》 | David Kennedy | 渗透测试 |
| 《白帽子讲 Web 安全》 | 吴翰清 | Web 安全 |
| 《Web 应用安全权威指南》 | Justin Clarke | Web 安全 |
| 《内网安全攻防》 | 徐焱 | 内网渗透 |
| 《逆向工程核心原理》 | 李承远 | 逆向工程 |
| 《密码编码学与网络安全》 | William Stallings | 密码学 |
| 《黑客攻防技术宝典》系列 | 多位 | 综合 |
| 《鸟哥的 Linux 私房菜》 | 鸟哥 | Linux 基础 |
| 《计算机网络》 | 谢希仁 | 网络基础 |

### 在线资源

| 资源 | 网址 | 说明 |
|------|------|------|
| Kali 官方文档 | https://www.kali.org/docs | 最权威 |
| OWASP | https://owasp.org | Web 安全标准 |
| PortSwigger Academy | https://portswigger.net/web-security | Web 安全实验 |
| HackTheBox | https://hackthebox.com | 渗透测试练习 |
| TryHackMe | https://tryhackme.com | 入门友好 |
| VulnHub | https://vulnhub.com | 虚拟机靶场 |
| Vulhub | https://github.com/vulhub/vulhub | Docker 靶场 |
| Exploit-DB | https://www.exploit-db.com | 漏洞数据库 |
| NVD | https://nvd.nist.gov | CVE 漏洞库 |
| CTFtime | https://ctftime.org | CTF 赛事 |

---

> **最后提醒：** 学习网络安全技术的目的是守护网络空间安全，而非破坏。请始终在合法、合规、授权的环境中使用 Kali Linux 和相关安全工具。技术是中性的，使用它的人决定了它的善恶。
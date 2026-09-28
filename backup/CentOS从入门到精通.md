> 全面涵盖安装、系统配置、命令行、YUM/DNF 包管理、网络配置、服务管理、存储管理、Shell 脚本、开发环境搭建、服务器运维、安全加固（SELinux）、性能优化、备份恢复及故障排查。

---

## 目录

- [一、CentOS 简介](#一centos-简介)
- [二、系统安装](#二系统安装)
- [三、初始配置](#三初始配置)
- [四、命令行基础](#四命令行基础)
- [五、文件与目录管理](#五文件与目录管理)
- [六、用户与权限管理](#六用户与权限管理)
- [七、软件包管理](#七软件包管理)
- [八、网络配置与管理](#八网络配置与管理)
- [九、服务与进程管理](#九服务与进程管理)
- [十、磁盘与存储管理](#十磁盘与存储管理)
- [十一、SELinux 安全管理](#十一selinux-安全管理)
- [十二、Shell 脚本编程](#十二shell-脚本编程)
- [十三、开发环境搭建](#十三开发环境搭建)
- [十四、服务器运维](#十四服务器运维)
- [十五、安全加固](#十五安全加固)
- [十六、性能优化与监控](#十六性能优化与监控)
- [十七、备份与恢复](#十七备份与恢复)
- [十八、故障排查](#十八故障排查)
- [十九、版本管理与迁移](#十九版本管理与迁移)
- [二十、完整实战示例](#二十完整实战示例)

---

## 一、CentOS 简介

### 什么是 CentOS

CentOS（Community ENTerprise Operating System）是基于 Red Hat Enterprise Linux（RHEL）源代码构建的免费企业级 Linux 发行版。它以**稳定性、安全性、长期支持**著称，是全球最广泛使用的服务器操作系统之一。

### CentOS 版本演进

| 版本 | 基于 RHEL | 发布时间 | EOL（停止维护） | 状态 |
|------|-----------|----------|-----------------|------|
| CentOS 6 | RHEL 6 | 2011 | 2020-11 | 已停止 |
| CentOS 7 | RHEL 7 | 2014 | 2024-06-30 | **已停止** |
| CentOS 8 | RHEL 8 | 2019 | 2021-12 | 已停止 |
| CentOS Stream 8 | RHEL 8 上游 | 2019 | 2024-05 | 已停止 |
| CentOS Stream 9 | RHEL 9 上游 | 2021 | 2027-05 | **当前主力** |
| CentOS Stream 10 | RHEL 10 上游 | 2024 | 预计 2030 | 开发中 |

> ⚠️ **重要变化：** CentOS 项目于 2020 年宣布战略转型。CentOS Linux 停止发布，转向 **CentOS Stream**（RHEL 的上游滚动发行版）。对于需要 RHEL 二进制兼容的企业用户，推荐迁移到 **Rocky Linux** 或 **AlmaLinux**。

### CentOS Stream vs 传统 CentOS

| 特性 | CentOS Linux 7（传统） | CentOS Stream |
|------|----------------------|---------------|
| 定位 | RHEL 的下游（克隆） | RHEL 的上游（预览） |
| 更新方式 | 稳定、滞后 | 滚动、领先 RHEL |
| 适用场景 | 生产服务器 | 开发、测试、前瞻 |
| 稳定性 | 极高 | 较高 |
| 软件新旧 | 较旧 | 较新 |

### 替代发行版（推荐）

| 发行版 | 定位 | 兼容性 | 支持周期 |
|--------|------|--------|----------|
| **Rocky Linux** | CentOS 继任者 | RHEL 二进制兼容 | 10 年 |
| **AlmaLinux** | CentOS 继任者 | RHEL 二进制兼容 | 10 年 |
| **Oracle Linux** | Oracle 版 RHEL | RHEL 二进制兼容 | 10 年 |
| **CentOS Stream** | RHEL 上游 | RHEL 兼容 | 5 年 |

### 适用场景

- 企业级 Web / 数据库 / 应用服务器
- 云计算与虚拟化平台
- 大数据与容器平台（Kubernetes / OpenStack）
- 需要长期稳定支持的生产环境
- 学习 RHEL 认证（RHCSA / RHCE / RHCA）

### 与 Ubuntu Server 对比

| 特性 | CentOS / RHEL | Ubuntu Server |
|------|---------------|---------------|
| 包管理 | YUM / DNF | APT |
| 服务管理 | systemd | systemd |
| 防火墙 | firewalld | UFW / iptables |
| 安全模块 | SELinux | AppArmor |
| 支持周期 | 10 年 | 5 年（LTS） |
| 软件新旧 | 较旧（稳定） | 较新 |
| 企业认证 | RHCE / RHCA | 无官方 |
| 云市场占有率 | 高 | 很高 |

### 官方资源

- CentOS 官网：https://www.centos.org
- CentOS 文档：https://docs.centos.org
- Rocky Linux：https://rockylinux.org
- AlmaLinux：https://almalinux.org
- RHEL 文档：https://access.redhat.com/documentation
- 软件包搜索：https://pkgs.org

---

## 二、系统安装

### 2.1 下载镜像

**CentOS Stream 9 / Rocky Linux 9 / AlmaLinux 9：**

| 发行版 | 下载地址 |
|--------|----------|
| CentOS Stream | https://www.centos.org/centos-stream |
| Rocky Linux | https://rockylinux.org/download |
| AlmaLinux | https://almalinux.org/get-almalinux |

**镜像类型：**

| 镜像 | 大小 | 用途 |
|------|------|------|
| DVD ISO | ~8-10GB | 完整安装（推荐离线安装） |
| Minimal ISO | ~1.5GB | 最小安装 |
| Boot ISO | ~700MB | 网络安装 |
| Everything ISO | ~15GB | 全部软件包 |

**验证镜像完整性：**
```bash
# 校验 SHA256
sha256sum -c CHECKSUM 2>&1 | grep -v 'FAILED' | grep OK

# 校验 GPG 签名
gpg --verify CHECKSUM.asc CHECKSUM
```

### 2.2 制作启动盘

```bash
# Linux 下使用 dd
lsblk                                      # 确认 U 盘
sudo dd if=CentOS-Stream-9-latest-x86_64-dvd1.iso of=/dev/sdX bs=4M status=progress
sync

# Windows 下使用 Rufus / balenaEtcher
# 选择 ISO → 选择 U 盘 → 开始
```

### 2.3 虚拟机安装（推荐入门）

**VMware Workstation：**
```
1. 新建虚拟机 → 典型
2. 选择"稍后安装操作系统"
3. 客户机操作系统：Linux → CentOS 8/9 64-bit（或 Red Hat Enterprise Linux 9 64-bit）
4. 磁盘 ≥ 40GB（服务器）/ ≥ 60GB（带桌面）
5. 内存 ≥ 2GB（服务器）/ ≥ 4GB（桌面），CPU ≥ 2 核
6. 网络：NAT / 桥接 / 仅主机
7. 选择 ISO → 开启虚拟机
```

**VirtualBox：**
```
1. 新建 → 类型：Linux，版本：Red Hat (64-bit) / CentOS (64-bit)
2. 内存 ≥ 2048MB
3. VDI 动态磁盘，≥ 40GB
4. 设置 → 存储 → 选择 ISO
5. 启动
```

### 2.4 安装步骤详解（Anaconda 安装器）

```
1. 启动菜单：
   - Install CentOS Stream 9（图形安装，推荐）
   - Test this media & install（先测试镜像）
   - Troubleshooting

2. 语言选择：中文（简体）或 English
   点击"继续"

3. 安装信息摘要（关键步骤）：
   ├── 本地化
   │   ├── 日期和时间：Asia/Shanghai
   │   ├── 键盘：Chinese / English (US)
   │   └── 语言支持：中文/英文
   │
   ├── 软件
   │   ├── 安装源：自动检测 ISO
   │   └── 软件选择：
   │       - 最小安装（服务器推荐）
   │       - 带 GUI 的服务器
   │       - GNOME 桌面
   │       - KDE Plasma 工作空间
   │       - 服务器（Web 服务器、计算节点等）
   │
   ├── 系统
   │   ├── 安装目的地：选择磁盘 + 分区方案
   │   │   - 自动配置分区（推荐新手）
   │   │   - 我要配置分区（手动）
   │   ├── KDUMP：启用（推荐）
   │   ├── 网络和主机名：
   │   │   - 打开网络接口
   │   │   - 设置主机名
   │   │   - 配置 IP（DHCP 或手动）
   │   ├── 安全策略：
   │   │   - 默认（Standard System Security Profile）
   │   │   - 可选 DISA STIG 等
   │   └── 系统目的（可选）：
   │       - 使用场景：生产/开发/测试
   │
   └── 用户设置
       ├── Root 密码：设置（牢记）
       └── 创建用户：用户名 + 密码

4. 点击"开始安装"
5. 等待安装完成 → 重启
```

**推荐分区方案（手动）：**
```
/boot       1GB       xfs     引导分区
/boot/efi   512MB     efi     EFI 分区（UEFI 引导，可选）
/           30-50GB   xfs     根分区
swap        内存 1-2 倍  swap  交换分区（内存 ≥ 16GB 可省略）
/var        20-30GB   xfs     日志与数据（服务器推荐）
/home       剩余空间   xfs     用户数据
```

### 2.5 Kickstart 自动安装（批量部署）

```bash
# 生成 Kickstart 文件
# 图形工具（在桌面环境中安装）
sudo yum install -y system-config-kickstart

# 或使用安装时生成的 anaconda-ks.cfg
cat /root/anaconda-ks.cfg
```

**Kickstart 示例（ks.cfg）：**
```
# 版本
# CentOS Stream 9

# 安装方式
install
cdrom

# 语言与键盘
lang zh_CN.UTF-8
keyboard --xlayouts='us'

# 网络
network --bootproto=dhcp --device=ens192 --activate
network --hostname=centos-server

# 防火墙与 SELinux
firewall --enabled --service=ssh
selinux --enforcing

# 认证
authselect --enableshadow --passalgo=sha512

# Root 密码（加密）
rootpw --iscrypted $6$xxxxxx

# 创建用户
user --name=admin --groups=wheel --iscrypted --password=$6$xxxxxx

# 时区
timezone Asia/Shanghai --utc

# 引导
bootloader --location=mbr --boot-drive=sda

# 分区
clearpart --all --initlabel
part /boot --fstype="xfs" --size=1024
part swap --fstype="swap" --size=4096
part / --fstype="xfs" --grow --size=1

# 软件包
%packages
@^minimal-environment
@core
vim-enhanced
wget
curl
git
%end

# 安装后脚本
%post
systemctl enable sshd
yum update -y
%end

# 重启
reboot
```

```bash
# 使用 Kickstart 安装
# 启动时在 GRUB 添加：
# inst.ks=hd:LABEL=USB:ks.cfg
# 或
# inst.ks=http://server/ks.cfg
```

### 2.6 云镜像安装

```bash
# AWS
# 使用 CentOS Stream 9 AMI 或 Rocky Linux 9 AMI

# Azure
# Azure Marketplace → 搜索 CentOS Stream 9

# GCP
# gcloud compute instances create centos-vm --image-family=centos-stream-9 --image-project=centos-cloud

# OpenStack / KVM
# 使用 qcow2 镜像
qemu-img create -f qcow2 -b base.qcow2 vm.qcow2
```

---

## 三、初始配置

### 3.1 配置 YUM/DNF 源

```bash
# CentOS 8+ / CentOS Stream 使用 DNF（YUM 的替代）
# 但 yum 命令仍然可用（软链接到 dnf）

# 查看当前源
yum repolist all
dnf repolist

# 备份原源配置
sudo cp -r /etc/yum.repos.d /etc/yum.repos.d.bak

# CentOS Stream 9 配置国内镜像源（阿里云）
sudo tee /etc/yum.repos.d/CentOS-Stream-BaseOS.repo << 'EOF'
[baseos]
name=CentOS Stream $releasever - BaseOS
baseurl=https://mirrors.aliyun.com/centos-stream/$releasever-stream/BaseOS/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
enabled=1
EOF

sudo tee /etc/yum.repos.d/CentOS-Stream-AppStream.repo << 'EOF'
[appstream]
name=CentOS Stream $releasever - AppStream
baseurl=https://mirrors.aliyun.com/centos-stream/$releasever-stream/AppStream/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
enabled=1
EOF

# 更新缓存
sudo yum clean all
sudo yum makecache
```

**Rocky Linux 9 国内源（阿里云）：**
```bash
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' \
    -i.bak \
    /etc/yum.repos.d/rocky-*.repo

sudo yum clean all && sudo yum makecache
```

**AlmaLinux 9 国内源（阿里云）：**
```bash
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=https://repo.almalinux.org|baseurl=https://mirrors.aliyun.com|g' \
    -i.bak \
    /etc/yum.repos.d/almalinux-*.repo

sudo yum clean all && sudo yum makecache
```

**添加 EPEL 源（Extra Packages for Enterprise Linux）：**
```bash
sudo yum install -y epel-release

# 或使用阿里云 EPEL
sudo yum install -y https://mirrors.aliyun.com/epel/epel-release-latest-9.noarch.rpm

sudo yum clean all && sudo yum makecache
```

### 3.2 系统更新

```bash
# 更新所有软件包
sudo yum update -y

# 查看可更新的包
yum check-update

# 只更新安全补丁
sudo yum update --security -y

# 查看更新历史
yum history
yum history info 12       # 查看第 12 条记录
yum history undo 12       # 撤销第 12 条操作

# 清理缓存
sudo yum clean all
sudo yum autoremove
```

### 3.3 设置主机名

```bash
# 查看
hostname
hostnamectl

# 修改
sudo hostnamectl set-hostname centos-server

# 修改 /etc/hosts
sudo nano /etc/hosts
# 127.0.1.1    centos-server
```

### 3.4 时间与时区

```bash
# 设置时区
sudo timedatectl set-timezone Asia/Shanghai

# 开启 NTP（CentOS 8+ 使用 chrony）
sudo yum install -y chrony
sudo systemctl enable chronyd
sudo systemctl start chronyd
sudo chronyc sources -v          # 查看 NTP 源
sudo chronyc tracking             # 查看同步状态

# CentOS 7 使用 ntpdate
sudo yum install -y ntpdate
sudo ntpdate ntp.aliyun.com

# 查看时间
timedatectl
date
```

### 3.5 关闭 SELinux / 配置 SELinux

```bash
# 查看 SELinux 状态
getenforce
sestatus

# 临时关闭（重启后恢复）
sudo setenforce 0

# 永久关闭（不推荐，建议学习管理 SELinux）
sudo nano /etc/selinux/config
# SELINUX=permissive    # 宽容模式（仅记录不阻止）
# SELINUX=disabled      # 完全禁用

# 重启生效
sudo reboot
```

### 3.6 关闭 / 配置防火墙 firewalld

```bash
# 查看防火墙状态
sudo systemctl status firewalld
sudo firewall-cmd --state

# 启动 / 停止
sudo systemctl start firewalld
sudo systemctl stop firewalld
sudo systemctl enable firewalld
sudo systemctl disable firewalld

# 查看规则
sudo firewall-cmd --list-all
sudo firewall-cmd --list-services
sudo firewall-cmd --list-ports

# 开放端口
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --permanent --add-port=22/tcp
sudo firewall-cmd --permanent --add-port=3306/tcp

# 开放服务
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=mysql

# 删除规则
sudo firewall-cmd --permanent --remove-port=3306/tcp
sudo firewall-cmd --permanent --remove-service=mysql

# 重新加载规则
sudo firewall-cmd --reload

# 富规则（精细控制）
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port protocol="tcp" port="3306" accept'
```

### 3.7 SSH 配置

```bash
# 安装 SSH（最小安装可能需要）
sudo yum install -y openssh-server

# 启动
sudo systemctl enable sshd
sudo systemctl start sshd

# 远程连接
ssh username@192.168.1.100

# 密钥登录
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh-copy-id username@192.168.1.100

# 客户端配置（~/.ssh/config）
Host myserver
    HostName 192.168.1.100
    User username
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

**SSH 安全配置（/etc/ssh/sshd_config）：**
```bash
sudo nano /etc/ssh/sshd_config

Port 2222                       # 修改端口
PermitRootLogin no              # 禁止 root 登录
PasswordAuthentication no       # 仅密钥登录
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2

sudo systemctl restart sshd

# 修改端口后更新防火墙
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --reload
```

### 3.8 中文支持

```bash
# 安装中文字体
sudo yum install -y wqy-microhei-fonts wqy-zenhei-fonts google-noto-cjk-fonts

# 安装中文输入法
sudo yum install -y ibus-libpinyin

# 设置系统语言
sudo localectl set-locale LANG=zh_CN.UTF-8

# 查看可用语言
localectl list-locales | grep zh
```

### 3.9 基础软件安装

```bash
sudo yum install -y \
    vim-enhanced \
    wget curl \
    git \
    net-tools \
    lsof \
    htop \
    tree \
    unzip zip \
    bash-completion \
    yum-utils \
    policycoreutils-python-utils \
    chrony \
    openssh-server
```

---

## 四、命令行基础

### 4.1 终端使用

```bash
# 快捷键
Ctrl + Alt + T        # 打开终端（桌面环境）
Ctrl + C              # 终止命令
Ctrl + Z              # 暂停任务
Ctrl + D              # 退出 / EOF
Ctrl + R              # 搜索历史
Ctrl + L              # 清屏
Ctrl + A / E          # 行首 / 行尾
Ctrl + U / K          # 删除行前 / 行后
Ctrl + W              # 删除前一个单词
Tab                   # 自动补全
!!                    # 重复上一条命令
sudo !!               # sudo 执行上一条
```

### 4.2 命令帮助

```bash
man command           # 手册
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
echo $SHELL
cat /etc/shells

# 安装 Zsh + Oh My Zsh
sudo yum install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh)
```

### 4.4 环境变量

```bash
echo $PATH
env
printenv HOME
export MY_VAR="val"

# 永久设置
echo 'export MY_VAR="val"' >> ~/.bashrc
source ~/.bashrc

# 系统级
sudo nano /etc/profile.d/custom.sh
# export MY_VAR="val"
```

---

## 五、文件与目录管理

### 5.1 文件系统结构

```
/                 根目录
├── bin -> usr/bin       基本命令（软链接）
├── boot                 引导文件
├── dev                  设备文件
├── etc                  系统配置文件
├── home                 用户主目录
├── lib -> usr/lib       系统库
├── media                可移动设备
├── mnt                  临时挂载点
├── opt                  第三方软件
├── proc                 进程信息（虚拟）
├── root                 root 家目录
├── run                  运行时数据
├── sbin -> usr/sbin     系统管理命令
├── srv                  服务数据
├── sys                  系统信息（虚拟）
├── tmp                  临时文件
├── usr                  用户程序
│   ├── bin              用户命令
│   ├── lib              库
│   ├── local            本地安装
│   ├── sbin             系统管理
│   └── share            共享数据
└── var                  可变数据
    ├── cache            缓存
    ├── log              日志
    ├── lib              状态数据
    └── www              Web 数据
```

### 5.2 CentOS 特有目录

```bash
/etc/yum.repos.d/             # YUM 仓库配置
/etc/firewalld/               # firewalld 配置
/etc/selinux/                 # SELinux 配置
/etc/sysconfig/               # 系统服务配置
/etc/ssh/sshd_config          # SSH 服务配置
/var/log/messages             # 系统日志
/var/log/secure               # 安全日志
/var/log/yum.log              # YUM 操作日志
/var/log/audit/audit.log      # SELinux 审计日志
/usr/share/man/               # man 手册
/etc/profile.d/               # 环境变量脚本
```

### 5.3 目录操作

```bash
pwd
ls -lha
ls -lt
ls -lS
ls -R

cd /path
cd ~
cd -
cd ..

mkdir dirname
mkdir -p a/b/c
rmdir dirname
rm -rf dirname
```

### 5.4 文件操作

```bash
touch file.txt
cp file1 file2
cp -a src/ dest/
mv file1 file2
rm file.txt
rm -i file.txt
rm -f file.txt
ln -s target link
ln target link
```

### 5.5 文件查看

```bash
cat file.txt
less file.txt
more file.txt
head -n 20 file.txt
tail -n 20 file.txt
tail -f file.txt
wc -l file.txt
file file.txt
stat file.txt
```

### 5.6 查找文件

```bash
# find
find / -name "*.conf"
find /etc -type f -name "*.conf"
find /var -type d -name "log*"
find / -size +100M
find / -mtime -7
find / -user nginx
find / -perm 755
find /tmp -name "*.tmp" -delete

# locate
sudo yum install -y mlocate
sudo updatedb
locate filename

# which / whereis
which python3
whereis nginx
```

### 5.7 文本处理

```bash
# grep
grep "keyword" file.txt
grep -r "keyword" /etc/
grep -i "keyword" file.txt
grep -n "keyword" file.txt
grep -v "keyword" file.txt
grep -c "keyword" file.txt
grep -E "error|warn" file.txt
grep -A 3 "error" file.txt

# sed
sed 's/old/new/g' file.txt
sed -i 's/old/new/g' file.txt
sed -n '5,10p' file.txt
sed '3d' file.txt

# awk
awk '{print $1}' file.txt
awk -F: '{print $1}' /etc/passwd
awk '$3 > 100' file.txt
awk '{sum+=$1} END {print sum}' file.txt

# sort / uniq
sort file.txt
sort -n file.txt
sort file.txt | uniq -c

# cut
cut -d: -f1 /etc/passwd
cut -c1-10 file.txt

# tr
echo "hello" | tr 'a-z' 'A-Z'
```

### 5.8 压缩与解压

```bash
tar -czvf a.tar.gz dir/
tar -xzvf a.tar.gz
tar -xjvf a.tar.bz2
tar -xJvf a.tar.xz
tar -tf a.tar.gz

zip -r a.zip dir/
unzip a.zip

gzip file
gunzip file.gz

# 7z
sudo yum install -y p7zip p7zip-plugins
7z x a.7z

# zstd
sudo yum install -y zstd
zstd file
zstd -d file.zst
```

---

## 六、用户与权限管理

### 6.1 用户管理

```bash
# 创建用户
sudo useradd -m -s /bin/bash username     # 命令式
sudo adduser username                      # CentOS 7 等价命令

# 设置密码
sudo passwd username

# 删除用户
sudo userdel -r username

# 修改用户
sudo usermod -l newname oldname
sudo usermod -d /new/home username
sudo usermod -s /bin/zsh username
sudo usermod -aG wheel username            # 加入 wheel 组（CentOS 的 sudo 组）

# 查看
whoami
who
w
id username
groups username
last
cat /etc/passwd
cat /etc/shadow                            # 密码哈希（需要 root）
```

### 6.2 用户组管理

```bash
sudo groupadd groupname
sudo groupdel groupname
sudo usermod -aG groupname username
sudo gpasswd -d username groupname
cat /etc/group | grep groupname
```

### 6.3 sudo 配置

```bash
# CentOS 使用 wheel 组管理 sudo
sudo usermod -aG wheel username

# 编辑 sudo 配置
sudo visudo

# 常用配置
# username ALL=(ALL:ALL) ALL                # 需要密码
# username ALL=(ALL:ALL) NOPASSWD: ALL      # 免密码
# %wheel ALL=(ALL:ALL) ALL                  # wheel 组所有成员
# %wheel ALL=(ALL) NOPASSWD: ALL            # wheel 组免密码

# CentOS 7 默认 wheel 组不需要密码
# 取消注释 %wheel 行即可
```

### 6.4 文件权限

```bash
# r(4) w(2) x(1)，所有者/组/其他
chmod 755 file
chmod 644 file
chmod +x script.sh
chmod -R 755 dir/
chmod u+x file
chmod g-w file

sudo chown user:group file
sudo chown -R user:group dir/
sudo chgrp groupname file

# 特殊权限
chmod u+s file                    # SUID
chmod g+s dir                     # SGID
chmod +t dir                      # Sticky bit

# umask
umask
umask 022

# ACL
getfacl file
setfacl -m u:username:rw file
setfacl -x u:username file

# 文件属性（chattr）
sudo chattr +i file               # 不可修改
sudo chattr +a file               # 只可追加
sudo chattr -i file               # 取消不可修改
lsattr file                       # 查看属性
```

---

## 七、软件包管理

### 7.1 YUM / DNF 包管理

```bash
# CentOS 7 使用 YUM
# CentOS 8+ / Stream / Rocky / Alma 使用 DNF（yum 是 dnf 的软链接）

# 基本操作
sudo yum update                              # 更新
sudo yum install package_name               # 安装
sudo yum install -y package_name            # 自动确认
sudo yum remove package_name                # 卸载
sudo yum erase package_name                 # 彻底卸载
sudo yum autoremove                         # 清理依赖
sudo yum clean all                          # 清理缓存

# 查询
yum search keyword
yum info package_name
yum list installed
yum list available
yum list updates
yum provides /path/to/file                  # 查找文件所属包
yum deplist package_name                    # 查看依赖
yum history                                 # 操作历史

# 组管理
yum grouplist                               # 列出包组
yum groupinfo "Development Tools"           # 查看包组信息
yum groupinstall "Development Tools"        # 安装包组
yum groupremove "Development Tools"         # 卸载包组

# 版本管理
yum install package_name-version            # 安装指定版本
yum --showduplicates list package_name      # 查看所有版本
sudo yum downgrade package_name             # 降级
sudo yum versionlock add package_name       # 锁定版本
sudo yum versionlock delete package_name    # 解锁

# 仅安全更新
sudo yum update --security
sudo yum update --security --bugfix
```

### 7.2 RPM 包管理

```bash
sudo rpm -ivh package.rpm                   # 安装
sudo rpm -Uvh package.rpm                   # 升级
sudo rpm -e package_name                    # 卸载
sudo rpm -qa                                # 已安装列表
rpm -qa | grep keyword                      # 查询
rpm -qi package_name                        # 包信息
rpm -ql package_name                        # 文件列表
rpm -qf /path/to/file                       # 查找文件所属包
rpm -qR package_name                        # 查看依赖
rpm -q --changelog package_name             # 变更日志
rpm -V package_name                         # 验证文件完整性
```

### 7.3 添加第三方仓库

```bash
# EPEL（Extra Packages for Enterprise Linux）
sudo yum install -y epel-release

# Remi（PHP 等新版软件）
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm

# RPM Fusion（多媒体）
sudo yum install -y https://mirrors.rpmfusion.org/free/el/rpmfusion-free-release-9.noarch.rpm
sudo yum install -y https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-9.noarch.rpm

# Nginx 官方仓库
sudo tee /etc/yum.repos.d/nginx.repo << 'EOF'
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
gpgkey=https://nginx.org/keys/nginx_signing.key
enabled=1
EOF

# MySQL 官方仓库
sudo yum install -y https://dev.mysql.com/get/mysql80-community-release-el9-6.noarch.rpm

# Docker 官方仓库
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# NodeSource（Node.js）
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -
```

### 7.4 源码编译安装

```bash
# 安装编译工具
sudo yum groupinstall -y "Development Tools"

# 编译安装流程
tar -xzf source.tar.gz
cd source
./configure --prefix=/usr/local/package_name
make -j$(nproc)
sudo make install

# 指定库路径（如果需要）
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH

# 卸载（如果有 make uninstall）
sudo make uninstall
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
nmcli device show | grep DNS

# 连接
ss -tlnp
ss -ulnp
ss -tunap
netstat -tlnp                  # 需安装 net-tools

# 流量
nload
iftop
nethogs
vnstat
```

### 8.2 网络配置方式

**方式一：nmcli（NetworkManager 命令行，CentOS 8+ 推荐）**

```bash
# 查看连接
nmcli device status
nmcli connection show

# 查看详细信息
nmcli device show eth0
nmcli connection show "System eth0"

# 配置静态 IP
nmcli connection modify "System eth0" ipv4.method manual
nmcli connection modify "System eth0" ipv4.addresses 192.168.1.100/24
nmcli connection modify "System eth0" ipv4.gateway 192.168.1.1
nmcli connection modify "System eth0" ipv4.dns "8.8.8.8 8.8.4.4 223.5.5.5"

# 配置 DHCP
nmcli connection modify "System eth0" ipv4.method auto

# 生效
nmcli connection up "System eth0"

# 修改主机名
nmcli general hostname centos-server

# WiFi
nmcli device wifi list
nmcli device wifi connect "SSID" password "password"

# 新建连接
nmcli connection add type ethernet con-name my-eth ifname eth0 ipv4.method manual ipv4.addresses 10.0.0.1/24

# 删除连接
nmcli connection delete my-eth
```

**方式二：nmtui（文本图形界面）**

```bash
nmtui
# 方向键 + Enter 导航
# 可以编辑连接、设置主机名、激活/停用连接
```

**方式三：传统配置文件（/etc/sysconfig/network-scripts/）**

```bash
sudo nano /etc/sysconfig/network-scripts/ifcfg-eth0
```

**DHCP 配置：**
```ini
TYPE=Ethernet
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes
```

**静态 IP 配置：**
```ini
TYPE=Ethernet
DEVICE=eth0
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
DNS3=223.5.5.5
```

```bash
# 重启网络
sudo systemctl restart NetworkManager
sudo nmcli connection up "System eth0"
# CentOS 7 也可以
sudo systemctl restart network
```

### 8.3 DNS 配置

```bash
# /etc/resolv.conf
sudo nano /etc/resolv.conf
# nameserver 8.8.8.8
# nameserver 8.8.4.4
# nameserver 223.5.5.5

# 通过 nmcli 配置（推荐，防止被覆盖）
nmcli connection modify "System eth0" ipv4.dns "8.8.8.8 8.8.4.4 223.5.5.5"
nmcli connection up "System eth0"

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

# 网卡信息
ip link show
ethtool eth0
```

### 8.5 防火墙（firewalld）

```bash
# CentOS 默认使用 firewalld（基于 nftables/iptables）

# 基本操作
sudo systemctl start firewalld
sudo systemctl stop firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --state

# 查看规则
sudo firewall-cmd --list-all
sudo firewall-cmd --list-all --zone=public
sudo firewall-cmd --get-zones
sudo firewall-cmd --list-services
sudo firewall-cmd --list-ports

# 端口管理
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --permanent --remove-port=80/tcp
sudo firewall-cmd --permanent --add-port=3000-4000/tcp    # 端口范围

# 服务管理
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --remove-service=telnet

# 富规则（Rich Rules）
# 允许特定 IP 访问
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.100" port protocol="tcp" port="3306" accept'

# 拒绝特定 IP
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.0.100" reject'

# 端口转发
sudo firewall-cmd --permanent --add-forward-port=port=80:proto=tcp:toport=8080
sudo firewall-cmd --permanent --add-masquerade     # 启用 NAT

# 区域管理
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --set-default-zone=dmz
sudo firewall-cmd --zone=dmz --add-interface=eth1

# 重新加载
sudo firewall-cmd --reload

# 查看所有规则
sudo firewall-cmd --list-all --permanent
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

# YUM 代理
sudo nano /etc/yum.conf
# proxy=http://127.0.0.1:7890

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

# 日志（journalctl）
journalctl -u service_name
journalctl -u service_name -f
journalctl -u service_name --since "1 hour ago"
journalctl -u service_name -n 100
journalctl -p err
journalctl --disk-usage
sudo journalctl --vacuum-size=100M

# CentOS 特有日志
tail -f /var/log/messages          # 系统日志
tail -f /var/log/secure            # 安全日志
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
User=nginx
Group=nginx
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
htop                               # 需安装 EPEL
pgrep -f "process_name"

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

# 查看资源使用
free -h
df -h
du -sh /path
uptime
```

### 9.4 定时任务（Cron）

```bash
crontab -e
crontab -l

# 系统级
sudo nano /etc/crontab

# cron 格式
# ┌──────────── 分钟 (0-59)
# │ ┌────────── 小时 (0-23)
# │ │ ┌──────── 日 (1-31)
# │ │ │ ┌────── 月 (1-12)
# │ │ │ │ ┌──── 星期 (0-7, 0和7=周日)
# * * * * * command

# 常用示例
0 2 * * * /root/backup.sh
*/5 * * * * /root/check.sh
0 0 * * 0 /root/weekly.sh

# 系统定时任务目录
/etc/cron.d/
/etc/cron.daily/
/etc/cron.hourly/
/etc/cron.weekly/
/etc/cron.monthly/
```

---

## 十、磁盘与存储管理

### 10.1 磁盘信息

```bash
df -h
df -i
du -sh /path
du -sh * | sort -hr

lsblk
sudo fdisk -l
sudo parted -l
blkid

# 磁盘性能
sudo hdparm -Tt /dev/sda
sudo yum install -y sysstat
iostat -xz 1
```

### 10.2 分区管理

```bash
# fdisk（MBR）
sudo fdisk /dev/sdb
# n 创建、d 删除、p 打印、w 保存

# parted（GPT）
sudo parted /dev/sdb
# mklabel gpt
# mkpart primary xfs 0% 50%
# print / quit

# 格式化（CentOS 默认使用 XFS）
sudo mkfs.xfs /dev/sdb1
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.vfat /dev/sdb1
sudo mkswap /dev/sdb2

# 调整 XFS
sudo xfs_repair /dev/sdb1
sudo xfs_growfs /mnt/data
```

### 10.3 挂载管理

```bash
sudo mount /dev/sdb1 /mnt/data
sudo mount -o ro /dev/sdb1 /mnt/data
sudo umount /mnt/data

# 永久挂载
sudo blkid
sudo nano /etc/fstab
# UUID=xxxx-xxxx  /mnt/data  xfs  defaults  0  2

sudo mount -a                       # 测试

# Swap
sudo swapon /dev/sdb2
sudo swapoff /dev/sdb2
swapon --show
free -h
```

### 10.4 LVM 逻辑卷

```bash
sudo yum install -y lvm2

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
sudo mkfs.xfs /dev/myvg/mydata
sudo mount /dev/myvg/mydata /mnt/data

# 扩容
sudo lvextend -L +10G /dev/myvg/mydata
sudo xfs_growfs /mnt/data            # XFS
# sudo resize2fs /dev/myvg/mydata    # ext4

# 缩容（仅 ext4）
sudo umount /mnt/data
sudo e2fsck -f /dev/myvg/mydata
sudo resize2fs /dev/myvg/mydata 15G
sudo lvreduce -L 15G /dev/myvg/mydata
sudo mount /dev/myvg/mydata /mnt/data
```

### 10.5 RAID 配置

```bash
sudo yum install -y mdadm

# 创建 RAID 1
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

# 查看
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# 保存配置
sudo mdadm --detail --scan >> /etc/mdadm.conf

# 格式化并挂载
sudo mkfs.xfs /dev/md0
sudo mount /dev/md0 /mnt/raid

# 故障处理
sudo mdadm /dev/md0 --fail /dev/sdb1
sudo mdadm /dev/md0 --remove /dev/sdb1
sudo mdadm /dev/md0 --add /dev/sdd1
```

### 10.6 Stratis / VDO（CentOS 8+ 新特性）

```bash
# Stratis（简化的存储管理）
sudo yum install -y stratisd stratis-cli
sudo systemctl enable --now stratisd

# 创建池
sudo stratis pool create mypool /dev/sdb

# 创建文件系统
sudo stratis filesystem create mypool myfs

# 挂载
sudo mount /dev/stratis/mypool/myfs /mnt/data

# 快照
sudo stratis filesystem snapshot mypool myfs myfs-snap

# VDO（数据压缩去重）
sudo yum install -y vdo
sudo vdo create --name=myvdo --device=/dev/sdb --vdoLogicalSize=50G
sudo mkfs.xfs /dev/mapper/myvdo
sudo mount /dev/mapper/myvdo /mnt/data
```

---

## 十一、SELinux 安全管理

### 11.1 SELinux 基础

```bash
# 查看状态
getenforce                # Enforcing / Permissive / Disabled
sestatus                  # 详细状态

# 临时切换模式
sudo setenforce 0         # Permissive（仅记录）
sudo setenforce 1         # Enforcing（强制执行）

# 永久配置
sudo nano /etc/selinux/config
# SELINUX=enforcing      # 强制模式（推荐）
# SELINUX=permissive     # 宽容模式
# SELINUX=disabled       # 禁用（不推荐）
```

### 11.2 SELinux 布尔值

```bash
# 查看所有布尔值
getsebool -a

# 查看特定布尔值
getsebool httpd_can_network_connect

# 设置布尔值（临时）
sudo setsebool httpd_can_network_connect 1

# 设置布尔值（永久）
sudo setsebool -P httpd_can_network_connect 1

# 常用布尔值
getsebool -a | grep httpd
# httpd_can_network_connect    允许 Nginx/Apache 网络连接
# httpd_enable_homedirs        允许访问用户家目录
# httpd_read_user_content      允许读取用户内容
```

### 11.3 SELinux 上下文

```bash
# 查看文件上下文
ls -Z /var/www/html/
ps -Z | grep nginx

# 修改文件上下文
sudo chcon -R -t httpd_sys_content_t /var/www/html/
sudo chcon -R -t httpd_sys_rw_content_t /var/www/html/uploads/

# 恢复默认上下文
sudo restorecon -Rv /var/www/html/

# 永久设置上下文（semanage）
sudo yum install -y policycoreutils-python-utils

# 添加文件上下文规则
sudo semanage fcontext -a -t httpd_sys_content_t "/myweb(/.*)?"
sudo restorecon -Rv /myweb

# 查看上下文规则
sudo semanage fcontext -l | grep myweb

# 删除规则
sudo semanage fcontext -d -t httpd_sys_content_t "/myweb(/.*)?"
```

### 11.4 SELinux 端口管理

```bash
# 查看端口上下文
sudo semanage port -l | grep http

# 添加端口
sudo semanage port -a -t http_port_t -p tcp 8080

# 修改端口
sudo semanage port -m -t http_port_t -p tcp 8080

# 删除端口
sudo semanage port -d -t http_port_t -p tcp 8080
```

### 11.5 SELinux 故障排查

```bash
# 查看 SELinux 拒绝日志
sudo ausearch -m avc -ts recent
sudo grep "denied" /var/log/audit/audit.log

# 使用 audit2allow 自动生成策略
sudo ausearch -m avc -ts recent | audit2allow -M mypolicy
sudo semodule -i mypolicy.pp

# 使用 setroubleshoot（推荐）
sudo yum install -y setroubleshoot setroubleshoot-server

# 查看 SELinux 问题建议
sudo sealert -a /var/log/audit/audit.log

# 临时禁用 SELinux 检查（排查用）
sudo setenforce 0
# 测试后记得恢复
sudo setenforce 1
```

### 11.6 SELinux 常见问题速查

| 问题 | 解决方案 |
|------|----------|
| Web 服务无法访问自定义目录 | `chcon -R -t httpd_sys_content_t /path` |
| Web 服务无法写入 | `chcon -R -t httpd_sys_rw_content_t /path` |
| 反向代理连接被拒 | `setsebool -P httpd_can_network_connect 1` |
| 自定义端口无法监听 | `semanage port -a -t http_port_t -p tcp 8080` |
| 文件被意外修改上下文 | `restorecon -Rv /path` |
| FTP 无法写入 | `setsebool -P ftpd_full_access 1` |
| MySQL 自定义端口 | `semanage port -a -t mysqld_port_t -p tcp 3307` |

---

## 十二、Shell 脚本编程

### 12.1 脚本基础

```bash
#!/bin/bash

# 变量
name="CentOS"
version=9
echo "Hello, $name $version"

# 只读变量
readonly PI=3.14159

# 特殊变量
$0 $1 $2 $3 $# $@ $? $$

# 字符串操作
str="Hello, World"
echo ${#str}
echo ${str:0:5}
echo ${str/World/Bash}
echo ${str^^}
echo ${str,,}

# 算术
a=10; b=3
echo $(( a + b ))
echo $(( a ** b ))
(( a++ ))
```

### 12.2 条件判断

```bash
# if
if [ "$x" -gt 10 ]; then
    echo "大于 10"
elif [ "$x" -eq 10 ]; then
    echo "等于 10"
else
    echo "小于 10"
fi

# 文件测试
[ -f file ]  [ -d dir ]  [ -e path ]
[ -r file ]  [ -w file ]  [ -x file ]  [ -s file ]

# 字符串
[ -z "$str" ]  [ -n "$str" ]
[ "$a" = "$b" ]  [ "$a" != "$b" ]

# 数值
[ "$a" -eq "$b" ]  [ "$a" -ne "$b" ]
[ "$a" -gt "$b" ]  [ "$a" -lt "$b" ]
[ "$a" -ge "$b" ]  [ "$a" -le "$b" ]

# 逻辑
[ cond1 ] && [ cond2 ]
[ cond1 ] || [ cond2 ]

# 双括号 / 双方括号
if (( x > 10 )); then fi
if [[ "$str" == hello* ]]; then fi

# case
case "$option" in
    start)  echo "启动" ;;
    stop)   echo "停止" ;;
    *)      echo "用法: $0 {start|stop}"; exit 1 ;;
esac
```

### 12.3 循环

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

### 12.4 函数

```bash
greet() {
    local name="$1"
    echo "Hello, $name!"
}
greet "Alice"

add() {
    local result=$(( $1 + $2 ))
    echo $result
}
sum=$(add 3 4)

check_file() {
    [ -f "$1" ] && return 0 || return 1
}

factorial() {
    if [ "$1" -le 1 ]; then
        echo 1
    else
        local prev=$(factorial $(( $1 - 1 )))
        echo $(( $1 * prev ))
    fi
}
```

### 12.5 实用脚本示例

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
cat /etc/redhat-release
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
echo "=== 防火墙 ==="
sudo firewall-cmd --list-all
echo ""
echo "=== SELinux ==="
getenforce
echo ""
echo "=== 失败服务 ==="
systemctl list-units --state=failed --no-pager
echo ""
echo "=== 最近登录 ==="
last -5
echo ""
echo "=== 安全更新 ==="
yum check-update --security 2>/dev/null | grep -v "^$" | wc -l
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
LOG="${1:-/var/log/messages}"
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

## 十三、开发环境搭建

### 13.1 开发工具

```bash
# 安装开发工具组
sudo yum groupinstall -y "Development Tools"

# 包含：gcc、g++、make、autoconf、automake、libtool 等

# 其他开发工具
sudo yum install -y cmake gdb valgrind
sudo yum install -y pkgconfig openssl-devel libcurl-devel libxml2-devel
```

### 13.2 Git

```bash
sudo yum install -y git

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

### 13.3 Python

```bash
# CentOS 9 自带 Python 3.9
python3 --version

# 安装 pip
sudo yum install -y python3-pip python3-devel

# 虚拟环境
python3 -m venv venv
source venv/bin/activate
pip install package_name

# pyenv（管理多版本）
curl https://pyenv.run | bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

### 13.4 Node.js

```bash
# NodeSource
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -
sudo yum install -y nodejs

# 或使用 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install --lts

# npm
npm install package_name
npm install -g package_name
```

### 13.5 Java

```bash
# 安装 JDK
sudo yum install -y java-17-openjdk java-17-openjdk-devel

# 查看版本
java -version
javac -version

# 设置 JAVA_HOME
echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc

# Maven
sudo yum install -y maven

# SDKMAN
curl -s "https://get.sdkman.io" | bash
sdk install java 21-tem
sdk install maven
```

### 13.6 Docker

```bash
# 添加 Docker 仓库
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 安装 Docker
sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 启动
sudo systemctl enable docker
sudo systemctl start docker

# 允许普通用户使用
sudo usermod -aG docker $USER
newgrp docker

# 验证
docker --version
docker run hello-world

# 配置镜像加速
sudo tee /etc/docker/daemon.json << 'EOF'
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

# 防火墙（如需外部访问 Docker）
sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
sudo firewall-cmd --permanent --zone=trusted --add-masquerade
sudo firewall-cmd --reload
```

### 13.7 数据库

```bash
# MySQL
sudo yum install -y mysql-server
sudo systemctl enable mysqld
sudo systemctl start mysqld
sudo mysql_secure_installation

# MariaDB（CentOS 默认）
sudo yum install -y mariadb-server mariadb
sudo systemctl enable mariadb
sudo systemctl start mariadb
sudo mysql_secure_installation

# PostgreSQL
sudo yum install -y postgresql-server postgresql-contrib
sudo postgresql-setup --initdb
sudo systemctl enable postgresql
sudo systemctl start postgresql

# Redis
sudo yum install -y redis
sudo systemctl enable redis
sudo systemctl start redis

# SQLite
sudo yum install -y sqlite

# MongoDB
sudo tee /etc/yum.repos.d/mongodb-org-7.0.repo << 'EOF'
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-7.0.asc
EOF
sudo yum install -y mongodb-org
```

---

## 十四、服务器运维

### 14.1 Web 服务器

**Nginx：**
```bash
# 安装（使用官方仓库获取新版）
sudo tee /etc/yum.repos.d/nginx.repo << 'EOF'
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
gpgkey=https://nginx.org/keys/nginx_signing.key
enabled=1
EOF

sudo yum install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx

# 配置
/etc/nginx/nginx.conf
/etc/nginx/conf.d/

# SELinux 允许 Nginx
sudo setsebool -P httpd_can_network_connect 1

# 防火墙
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload

# 测试
curl -I http://localhost
```

**Apache：**
```bash
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# 配置
/etc/httpd/conf/httpd.conf
/etc/httpd/conf.d/

# SELinux
sudo setsebool -P httpd_can_network_connect 1

# 防火墙
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

### 14.2 SSL 证书（Let's Encrypt）

```bash
# 安装 Certbot
sudo yum install -y certbot python3-certbot-nginx

# Nginx
sudo certbot --nginx -d example.com -d www.example.com

# Apache
sudo yum install -y python3-certbot-apache
sudo certbot --apache -d example.com

# 自动续期
sudo certbot renew --dry-run

# 添加定时续期
echo "0 0,12 * * * root python3 -c 'import random; import time; time.sleep(random.random() * 43200)' && certbot renew -q" | sudo tee -a /etc/crontab > /dev/null

# 查看证书
sudo certbot certificates
```

### 14.3 反向代理配置

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

### 14.4 日志管理

```bash
# CentOS 日志位置
/var/log/messages          # 系统日志
/var/log/secure            # 安全日志
/var/log/cron              # 定时任务日志
/var/log/maillog           # 邮件日志
/var/log/yum.log           # YUM 操作日志
/var/log/audit/audit.log   # SELinux 审计日志
/var/log/lastlog           # 最后登录日志

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

# 查看日志
tail -f /var/log/messages
journalctl -f
journalctl --since "2024-01-15"
```

### 14.5 定时任务运维

```bash
# 常见定时任务
crontab -e

# 每天凌晨 2 点备份
0 2 * * * /root/backup.sh >> /var/log/backup.log 2>&1

# 每 5 分钟检查服务
*/5 * * * * /usr/bin/systemctl is-active nginx > /dev/null 2>&1 || /usr/bin/systemctl restart nginx

# 每周日凌晨 3 点更新
0 3 * * 0 /usr/bin/yum update -y >> /var/log/yum-update.log 2>&1

# 每月 1 号清理日志
0 0 1 * * /usr/bin/journalctl --vacuum-size=100M

# 查看 cron 日志
tail -f /var/log/cron
```

---

## 十五、安全加固

### 15.1 系统更新策略

```bash
# 启用自动安全更新
sudo yum install -y yum-cron

# 配置
sudo nano /etc/yum/yum-cron.conf
# update_cmd = security          # 只安装安全更新
# apply_updates = yes            # 自动安装
# download_updates = yes

sudo systemctl enable yum-cron
sudo systemctl start yum-cron

# 手动检查安全更新
sudo yum update --security
```

### 15.2 SSH 安全加固

```bash
sudo nano /etc/ssh/sshd_config

Port 2222
PermitRootLogin no
PasswordAuthentication no
MaxAuthTries 3
LoginGraceTime 30
ClientAliveInterval 300
AllowUsers username
X11Forwarding no

sudo systemctl restart sshd

# 更新防火墙
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --reload
```

### 15.3 防火墙配置

```bash
# firewalld 默认策略
sudo firewall-cmd --set-default-zone=public

# 最小开放原则
sudo firewall-cmd --permanent --remove-service=cockpit
sudo firewall-cmd --permanent --remove-service=dhcpv6-client
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

### 15.4 Fail2Ban（防暴力破解）

```bash
sudo yum install -y epel-release
sudo yum install -y fail2ban fail2ban-firewalld

# 配置
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local

# [DEFAULT]
# bantime = 3600
# findtime = 600
# maxretry = 5
# banaction = firewallcmd-rich-rules[actiontype=<multiport>]

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

### 15.5 密码策略

```bash
# 修改密码老化策略
sudo nano /etc/login.defs
# PASS_MAX_DAYS 90
# PASS_MIN_DAYS 7
# PASS_MIN_LEN 12
# PASS_WARN_AGE 14

# 使用 authconfig（CentOS 7）
sudo authconfig --passminlen=12 --passminclass=3 --update

# 使用 authselect（CentOS 8+）
sudo authselect select sssd with-faillock with-pamaccess
```

### 15.6 内核安全参数

```bash
sudo nano /etc/sysctl.d/99-security.conf
```
```
# 禁止 IP 转发
net.ipv4.ip_forward = 0

# 禁止 ICMP 重定向
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0

# 禁止源路由
net.ipv4.conf.all.accept_source_route = 0

# 忽略 ICMP 广播
net.ipv4.icmp_echo_ignore_broadcasts = 1

# 开启 SYN Cookie（防 SYN Flood）
net.ipv4.tcp_syncookies = 1

# ASLR（地址空间随机化）
kernel.randomize_va_space = 2

# 禁止 core dump
fs.suid_dumpable = 0

# 记录异常数据包
net.ipv4.conf.all.log_martians = 1
```
```bash
sudo sysctl --system
```

### 15.7 文件完整性检查

```bash
# AIDE（Advanced Intrusion Detection Environment）
sudo yum install -y aide
sudo aideinit
sudo cp /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz

# 检查
sudo aide --check

# 更新基线
sudo aide --update
sudo cp /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
```

### 15.8 审计系统（auditd）

```bash
sudo yum install -y audit
sudo systemctl enable auditd
sudo systemctl start auditd

# 添加审计规则
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
sudo auditctl -w /etc/shadow -p wa -k shadow_changes
sudo auditctl -w /etc/sudoers -p wa -k sudoers_changes

# 查看审计日志
sudo ausearch -k passwd_changes
sudo aureport --auth                # 认证报告
sudo aureport --file                # 文件访问报告

# 永久审计规则
sudo nano /etc/audit/rules.d/audit.rules
# -w /etc/passwd -p wa -k passwd_changes

sudo systemctl restart auditd
```

---

## 十六、性能优化与监控

### 16.1 监控工具

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
glances            # sudo yum install glances
nmon               # sudo yum install nmon

# sysstat（历史统计）
sudo yum install -y sysstat
sudo systemctl enable sysstat
sudo systemctl start sysstat
sar -u
sar -r
sar -d
sar -n DEV
```

### 16.2 性能优化

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
net.core.netdev_max_backlog = 65535

# 内存
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5

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
sudo yum install -y tuned
sudo tuned-adm active
sudo tuned-adm profile throughput-performance
sudo tuned-adm list
```

### 16.3 调优工具（tuned）

```bash
# tuned 是 RHEL/CentOS 的自动调优工具
sudo yum install -y tuned
sudo systemctl enable tuned
sudo systemctl start tuned

# 查看可用 profile
tuned-adm list

# 设置 profile
sudo tuned-adm profile throughput-performance     # 高吞吐
sudo tuned-adm profile latency-performance        # 低延迟
sudo tuned-adm profile balanced                   # 平衡（默认）
sudo tuned-adm profile virtual-host               # 虚拟化宿主机
sudo tuned-adm profile virtual-guest              # 虚拟化客户机

# 查看当前配置
tuned-adm active
tuned-adm recommend

# 自定义 profile
sudo cp -r /usr/lib/tuned/profiles/balanced /etc/tuned/myprofile
sudo nano /etc/tuned/myprofile/tuned.conf
sudo tuned-adm profile myprofile
```

### 16.4 日志优化

```bash
sudo nano /etc/systemd/journald.conf
# SystemMaxUse=200M
# MaxRetentionSec=1month

sudo systemctl restart systemd-journald
sudo journalctl --vacuum-size=100M

# rsyslog 配置
sudo nano /etc/rsyslog.conf
# 可以配置远程日志、日志过滤等

sudo systemctl restart rsyslog
```

---

## 十七、备份与恢复

### 17.1 rsync 备份

```bash
sudo yum install -y rsync

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

# 增量备份
rsync -avz --delete \
    --link-dest=/backup/previous \
    /source/ /backup/current/

# 演练
rsync -avzn /source/ /backup/
```

### 17.2 dump / restore（文件系统级备份）

```bash
sudo yum install -y dump

# 完整备份
sudo dump -0uf /backup/root.dump /dev/sda1

# 增量备份
sudo dump -1uf /backup/root.dump.1 /dev/sda1

# 恢复
sudo restore -rf /backup/root.dump

# 交互式恢复
sudo restore -if /backup/root.dump
```

### 17.3 dd 磁盘备份

```bash
# 创建磁盘镜像
sudo dd if=/dev/sda of=/backup/disk.img bs=4M status=progress

# 恢复磁盘镜像
sudo dd if=/backup/disk.img of=/dev/sda bs=4M status=progress

# 克隆磁盘
sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress

# 压缩备份
sudo dd if=/dev/sda bs=4M | gzip > /backup/disk.img.gz

# 恢复压缩备份
gunzip -c /backup/disk.img.gz | sudo dd of=/dev/sda bs=4M
```

### 17.4 备份策略

```
3-2-1 备份原则：
- 3 份数据副本
- 2 种不同存储介质
- 1 份异地备份

计划：
├── 每日：增量备份（rsync / 数据库 dump）
├── 每周：完整备份
├── 每月：归档备份（异地）
└── 每次变更前：磁盘快照（LVM / 虚拟机快照）
```

### 17.5 数据库备份

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

# LVM 快照备份（在线备份）
sudo lvcreate -L 5G -s -n db_snap /dev/myvg/mysqldata
sudo mount /dev/myvg/db_snap /mnt/snapshot
sudo rsync -avz /mnt/snapshot/ /backup/mysql/
sudo umount /mnt/snapshot
sudo lvremove /dev/myvg/db_snap
```

---

## 十八、故障排查

### 18.1 启动问题

```bash
# 无法进入图形界面
# Ctrl + Alt + F2 → TTY
sudo systemctl status gdm3
sudo systemctl restart gdm3
sudo yum groupinstall -y "GNOME Desktop"

# GRUB 修复
# Live USB 启动
sudo mount /dev/sdaX /mnt
sudo mount /dev/sdaY /mnt/boot/efi
sudo grub2-install --root-directory=/mnt /dev/sda
sudo chroot /mnt grub2-mkconfig -o /boot/grub2/grub.cfg

# 恢复模式
# GRUB → Troubleshooting → Rescue a CentOS system

# 忘记 root 密码
# GRUB 编辑启动项 → 在 linux 行末尾添加 rd.break
# 按 Ctrl+X 启动
# mount -o remount,rw /sysroot
# chroot /sysroot
# passwd
# touch /.autorelabel    # 如果启用了 SELinux
# exit && reboot

# init=/bin/bash 方式
# GRUB 编辑 → linux 行末尾加 init=/bin/bash
# mount -o remount,rw /
# passwd
# sync && exec /sbin/init
```

### 18.2 网络问题

```bash
ip addr show
ip route show
ping -c 4 8.8.8.8

# DNS
nslookup google.com
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# NetworkManager
sudo systemctl restart NetworkManager
nmcli device status
nmcli connection up "System eth0"

# 防火墙
sudo firewall-cmd --list-all
sudo firewall-cmd --state

# SELinux 阻止网络
sudo setenforce 0           # 临时关闭测试
# 如果问题消失，说明是 SELinux 的问题
```

### 18.3 磁盘问题

```bash
df -h
df -i
du -sh /var/log/*

# 清理
sudo journalctl --vacuum-size=100M
sudo yum clean all
sudo yum autoremove

# 文件系统检查
sudo xfs_repair /dev/sda1        # XFS
sudo e2fsck -f /dev/sda1         # ext4

# 只读问题
dmesg | grep -i error
sudo mount -o remount,rw /
```

### 18.4 软件问题

```bash
# 依赖问题
sudo yum install -y package_name --allowerasing
sudo yum distro-sync

# GPG 错误
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial

# 仓库缓存
sudo yum clean all
sudo yum makecache

# 包冲突
sudo yum remove conflicting_package
sudo yum reinstall package_name

# RPM 数据库损坏
sudo rpm --rebuilddb
```

### 18.5 SELinux 问题

```bash
# 查看拒绝日志
sudo ausearch -m avc -ts recent
sudo grep "denied" /var/log/audit/audit.log

# 自动生成策略
sudo ausearch -m avc -ts recent | audit2allow -M mypolicy
sudo semodule -i mypolicy.pp

# setroubleshoot
sudo sealert -a /var/log/audit/audit.log

# 临时关闭测试
sudo setenforce 0
```

### 18.6 常见错误速查

| 现象 | 可能原因 | 解决方案 |
|------|----------|----------|
| `Permission denied` | 权限 / SELinux | `chmod` / `chcon` |
| `Command not found` | 未安装 | `yum install` |
| `No space left` | 磁盘满 | `df -h` 清理 |
| `Connection refused` | 服务/防火墙 | `systemctl start` / `firewall-cmd` |
| `Could not resolve host` | DNS | 修改 resolv.conf |
| `RPM database error` | RPM 数据库损坏 | `rpm --rebuilddb` |
| `YUM repo error` | 源配置错误 | 检查 repo 文件 |
| `SELinux denial` | SELinux 阻止 | `audit2allow` / `semanage` |
| `Boot error` | GRUB 损坏 | Live USB 修复 |
| `Failed to start service` | 配置错误 | `journalctl -u service` |

**Magic SysRq（强制恢复）：**
```bash
echo 1 | sudo tee /proc/sys/kernel/sysrq
# Alt + SysRq + R, E, I, S, U, B（安全重启顺序）
```

---

## 十九、版本管理与迁移

### 19.1 版本查看

```bash
cat /etc/redhat-release          # CentOS Linux release 7.x / CentOS Stream release 9
cat /etc/os-release
hostnamectl
uname -r
uname -a
rpm -q centos-release            # CentOS
rpm -q rocky-release             # Rocky Linux
rpm -q almalinux-release         # AlmaLinux
```

### 19.2 CentOS 7 → Rocky/Alma 8 迁移

```bash
# 使用迁移工具（ELevate / almalinux-deploy / rocky-migrate2rocky）

# AlmaLinux 迁移
curl -O https://raw.githubusercontent.com/AlmaLinux/almalinux-deploy/master/almalinux-deploy.sh
sudo bash almalinux-deploy.sh

# Rocky Linux 迁移
curl -O https://raw.githubusercontent.com/rocky-linux/rocky-tools/main/migrate2rocky/migrate2rocky.sh
sudo bash migrate2rocky.sh -r

# CentOS 7 → 8 迁移（ELevate）
sudo yum install -y https://repo.almalinux.org/elevate/elevate-release-latest-el7.noarch.rpm
sudo yum install -y leapp-upgrade leapp-data-rocky
sudo leapp preupgrade
sudo leapp upgrade
sudo reboot
```

### 19.3 跨版本升级（CentOS Stream 8 → 9）

```bash
# 1. 更新当前系统
sudo yum update -y

# 2. 安装升级工具
sudo yum install -y leapp-upgrade leapp-data-rocky

# 3. 预升级检查
sudo leapp preupgrade

# 4. 修复预升级报告中的问题
# 查看 /var/log/leapp/leapp-report.txt

# 5. 执行升级
sudo leapp upgrade

# 6. 重启
sudo reboot

# 7. 验证
cat /etc/redhat-release
```

### 19.4 内核管理

```bash
# 查看内核
uname -r

# 已安装内核
rpm -qa | grep kernel

# 安装新内核
sudo yum install -y kernel

# 删除旧内核
sudo yum install -y yum-utils
sudo package-cleanup --oldkernels --count=1

# 查看启动参数
cat /proc/cmdline

# 修改 GRUB 默认启动项
sudo grub2-set-default 0
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
# UEFI
sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

# 查看 GRUB 菜单项
sudo grubby --info=ALL
sudo grubby --default-kernel
```

---

## 二十、完整实战示例

### 20.1 Web 应用部署脚本

```bash
#!/bin/bash
# deploy_webapp.sh
set -e

APP_NAME="myapp"
APP_DIR="/opt/$APP_NAME"
NGINX_CONF="/etc/nginx/conf.d/$APP_NAME.conf"
DOMAIN="example.com"

echo "=== 部署 $APP_NAME ==="

# 1. 安装依赖
echo "[1/7] 安装依赖..."
sudo yum update -y
sudo yum install -y nginx git python3 python3-pip certbot python3-certbot-nginx

# 2. 克隆代码
echo "[2/7] 克隆代码..."
sudo mkdir -p "$APP_DIR"
sudo git clone https://github.com/user/$APP_NAME.git "$APP_DIR"

# 3. 配置 Python
echo "[3/7] 配置 Python..."
cd "$APP_DIR"
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
deactivate

# 4. 创建 systemd 服务
echo "[4/7] 创建服务..."
sudo tee /etc/systemd/system/$APP_NAME.service > /dev/null << EOF
[Unit]
Description=$APP_NAME
After=network.target

[Service]
Type=simple
User=nginx
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
echo "[5/7] 配置 Nginx..."
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

sudo nginx -t
sudo systemctl reload nginx

# 6. 配置防火墙和 SELinux
echo "[6/7] 配置防火墙和 SELinux..."
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload

sudo setsebool -P httpd_can_network_connect 1
sudo chcon -R -t httpd_sys_content_t "$APP_DIR/static" 2>/dev/null || true

# 7. 配置 SSL
echo "[7/7] 配置 SSL..."
sudo certbot --nginx -d "$DOMAIN" --non-interactive --agree-tos --email admin@$DOMAIN

echo "=== 部署完成 ==="
echo "访问: https://$DOMAIN"
```

### 20.2 服务器初始化脚本

```bash
#!/bin/bash
# init_server.sh - CentOS 服务器初始化
set -e

echo "=== CentOS 服务器初始化 ==="

# 更新系统
echo "[1/10] 更新系统..."
sudo yum update -y

# 设置时区
echo "[2/10] 设置时区..."
sudo timedatectl set-timezone Asia/Shanghai

# 配置 NTP
echo "[3/10] 配置时间同步..."
sudo yum install -y chrony
sudo systemctl enable chronyd
sudo systemctl start chronyd

# 创建 sudo 用户
echo "[4/10] 配置 sudo 用户..."
read -p "输入新用户名: " NEW_USER
sudo useradd -m -s /bin/bash "$NEW_USER"
sudo passwd "$NEW_USER"
sudo usermod -aG wheel "$NEW_USER"

# 安装基础软件
echo "[5/10] 安装基础软件..."
sudo yum install -y \
    vim-enhanced wget curl git net-tools lsof \
    htop tree unzip zip bash-completion \
    yum-utils chrony openssh-server \
    policycoreutils-python-utils

# 配置防火墙
echo "[6/10] 配置防火墙..."
sudo firewall-cmd --set-default-zone=public
sudo firewall-cmd --permanent --add-port=22/tcp
sudo firewall-cmd --reload

# 加固 SSH
echo "[7/10] 加固 SSH..."
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/#MaxAuthTries 6/MaxAuthTries 3/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# 配置 Fail2Ban
echo "[8/10] 配置 Fail2Ban..."
sudo yum install -y epel-release
sudo yum install -y fail2ban fail2ban-firewalld
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# 配置自动安全更新
echo "[9/10] 配置自动安全更新..."
sudo yum install -y yum-cron
sudo sed -i 's/update_cmd = default/update_cmd = security/' /etc/yum/yum-cron.conf
sudo sed -i 's/apply_updates = no/apply_updates = yes/' /etc/yum/yum-cron.conf
sudo systemctl enable yum-cron
sudo systemctl start yum-cron

# 优化内核
echo "[10/10] 优化内核参数..."
sudo tee /etc/sysctl.d/99-tuning.conf > /dev/null << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_syncookies = 1
vm.swappiness = 10
fs.file-max = 2097152
kernel.randomize_va_space = 2
EOF
sudo sysctl --system

# tuned 性能调优
sudo yum install -y tuned
sudo systemctl enable tuned
sudo tuned-adm profile throughput-performance

echo ""
echo "=== 初始化完成 ==="
echo "下一步："
echo "1. 重新登录: ssh $NEW_USER@$(hostname -I | awk '{print $1}')"
echo "2. 配置 SSH 密钥登录"
echo "3. 重启系统"
```

### 20.3 Docker + Compose 环境部署

```bash
#!/bin/bash
# setup_docker.sh
set -e

echo "=== 安装 Docker ==="

# 安装依赖
sudo yum install -y yum-utils

# 添加 Docker 仓库
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 安装
sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 配置用户
sudo usermod -aG docker $USER

# 配置 Docker
sudo tee /etc/docker/daemon.json > /dev/null << 'EOF'
{
  "registry-mirrors": [
    "https://mirror.ccs.tencentyun.com",
    "https://docker.mirrors.ustc.edu.cn"
  ],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2"
}
EOF

# 启动
sudo systemctl enable docker
sudo systemctl start docker

# 防火墙配置
sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
sudo firewall-cmd --permanent --zone=trusted --add-masquerade
sudo firewall-cmd --reload

echo "=== Docker 安装完成 ==="
docker --version
docker compose version
echo "请重新登录以使 docker 组生效"
```

---

## 附录 A：CentOS 特有命令速查

| 命令 | 说明 |
|------|------|
| `yum install/remove/update` | 软件包管理 |
| `yum groupinstall` | 安装包组 |
| `yum history` | 查看/撤销操作历史 |
| `rpm -qa/-qi/-ql/-qf` | RPM 包查询 |
| `firewall-cmd` | firewalld 防火墙管理 |
| `getenforce / setenforce` | SELinux 模式管理 |
| `semanage` | SELinux 策略管理 |
| `restorecon` | 恢复文件 SELinux 上下文 |
| `audit2allow` | 从审计日志生成策略 |
| `nmcli` / `nmtui` | NetworkManager 管理 |
| `tuned-adm` | 系统性能调优 |
| `authselect` | 认证配置 |
| `grubby` | GRUB 启动项管理 |
| `yum-cron` | 自动安全更新 |
| `chronyc` | 时间同步管理 |

## 附录 B：推荐学习资源

### 书籍

| 书名 | 作者 | 方向 |
|------|------|------|
| 《RHCSA/RHCE Red Hat Linux 认证学习指南》 | Sander van Vugt | RHEL 认证 |
| 《Linux 就该这么学》 | 刘遄 | Linux 入门 |
| 《鸟哥的 Linux 私房菜》 | 鸟哥 | Linux 基础 |
| 《Linux 命令行与 Shell 脚本编程大全》 | Richard Blum | Shell 脚本 |
| 《Linux 性能优化实战》 | 倪朋飞 | 性能优化 |
| 《SELinux by Example》 | Frank Mayer | SELinux |

### 在线资源

| 资源 | 网址 | 说明 |
|------|------|------|
| CentOS 文档 | https://docs.centos.org | 官方文档 |
| Rocky Linux 文档 | https://docs.rockylinux.org | Rocky 官方 |
| AlmaLinux 文档 | https://wiki.almalinux.org | Alma 官方 |
| Red Hat 文档 | https://access.redhat.com/documentation | RHEL 文档 |
| Arch Wiki | https://wiki.archlinux.org | 通用 Linux 知识 |
| DigitalOcean 教程 | https://www.digitalocean.com/community/tutorials | 实用教程 |
| Linux Journey | https://linuxjourney.com | 交互式学习 |
| OverTheWire | https://overthewire.org | 安全学习 |

---

> **总结：** CentOS / RHEL 系列是企业级 Linux 的标杆，以稳定性、安全性和长达 10 年的支持周期著称。掌握 YUM/DNF 包管理、firewalld 防火墙、SELinux 安全模块和 systemd 服务管理，是运维 CentOS 的核心技能。随着 CentOS Linux 停止维护，Rocky Linux 和 AlmaLinux 是最佳替代选择，它们保持了与 RHEL 的二进制兼容性。学习 RHEL 生态的技能，在企业 IT 领域有着极高的价值。
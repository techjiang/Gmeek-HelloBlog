下面按功能分类整理，适用于大多数主流发行版（CentOS/Ubuntu/Debian 等）。

---

## 一、文件与目录操作

| 命令 | 说明 | 示例 |
|------|------|------|
| `ls` | 列出目录内容 | `ls -lha` 显示详细信息+隐藏文件 |
| `cd` | 切换目录 | `cd /usr/local` |
| `pwd` | 显示当前路径 | `pwd` |
| `mkdir` | 创建目录 | `mkdir -p a/b/c` 递归创建 |
| `rmdir` | 删除空目录 | `rmdir dir` |
| `rm` | 删除文件/目录 | `rm -rf dir` 强制递归删除 |
| `cp` | 复制 | `cp -a src/ dest/` 保留属性 |
| `mv` | 移动/重命名 | `mv old.txt new.txt` |
| `touch` | 创建空文件/更新时间戳 | `touch file.txt` |
| `ln` | 创建链接 | `ln -s /path/file link` 软链接 |
| `find` | 查找文件 | `find / -name "*.log"` |
| `locate` | 快速查找（需先 `updatedb`） | `locate nginx.conf` |
| `tree` | 树形显示目录 | `tree -L 2` 显示两层 |

---

## 二、文件查看与编辑

| 命令 | 说明 |
|------|------|
| `cat file` | 显示全部内容 |
| `tac file` | 反向显示（从末行开始） |
| `less file` | 分页查看（支持搜索 `/关键字`） |
| `more file` | 分页查看（只能向下） |
| `head -n 20 file` | 查看前 20 行 |
| `tail -n 20 file` | 查看后 20 行 |
| `tail -f file` | **实时追踪文件更新**（看日志必备） |
| `wc -l file` | 统计行数 |
| `nano / vi / vim` | 文本编辑器 |

**vim 常用操作：**
```
i            进入编辑模式
Esc          退出编辑模式
:wq          保存并退出
:q!          强制退出不保存
dd           删除整行
yy + p       复制行 + 粘贴
/关键字      搜索
:set nu      显示行号
```

---

## 三、权限管理

```bash
chmod 755 file          # rwxr-xr-x（所有者/组/其他）
chmod +x script.sh      # 添加执行权限
chown user:group file   # 修改所有者和组
chown -R nginx:nginx /var/www   # 递归修改
umask 022               # 设置默认权限掩码
```

**权限数字对照：** `r=4, w=2, x=1`，组合成所有者/组/其他三位数字。

---

## 四、用户与用户组

```bash
useradd -m -s /bin/bash username   # 创建用户并建家目录
passwd username                     # 设置密码
userdel -r username                 # 删除用户及家目录
usermod -aG sudo username           # 将用户加入附加组

groupadd groupname                  # 创建用户组
groupdel groupname                  # 删除用户组
id username                         # 查看用户 UID/GID 和所属组
whoami                              # 当前登录用户
last                                # 查看登录记录
```

---

## 五、压缩与解压

```bash
tar -czvf a.tar.gz dir/       # 打包并 gzip 压缩
tar -xzvf a.tar.gz            # 解压 .tar.gz
tar -xjvf a.tar.bz2           # 解压 .tar.bz2
tar -xJvf a.tar.xz            # 解压 .tar.xz

zip -r a.zip dir/             # 打包为 zip
unzip a.zip                   # 解压 zip

gzip file                     # 压缩（原文件被替换）
gunzip file.gz                # 解压
```

> 记忆口诀：**c 创建、x 解压、z gzip、j bz2、J xz、v 显示过程、f 指定文件**

---

## 六、进程管理

```bash
ps aux                        # 查看所有进程
ps -ef | grep nginx           # 查找指定进程
top                           # 实时资源监控（按 q 退出）
htop                          # 更美观的 top（需安装）
kill PID                      # 终止进程
kill -9 PID                   # 强制终止
killall nginx                 # 按名称终止
nohup ./app.sh &              # 后台运行，退出终端不中断
jobs / fg / bg                # 任务管理
```

**查看资源：**
```bash
free -h          # 内存使用情况
df -h            # 磁盘使用情况
du -sh *         # 当前目录各文件夹大小
uptime           # 负载情况
```

---

## 七、网络相关

```bash
ip addr                     # 查看 IP（推荐）
ifconfig                    # 查看 IP（旧命令）
ping baidu.com              # 测试连通性
curl -I https://baidu.com   # 查看 HTTP 响应头
wget https://xxx/file.zip   # 下载文件
netstat -tunlp              # 查看端口占用
ss -tunlp                   # 查看端口占用（推荐）
lsof -i:80                  # 查看 80 端口占用
traceroute baidu.com        # 路由追踪
nslookup baidu.com          # DNS 查询
dig baidu.com               # DNS 查询（更详细）
scp file user@host:/path    # 远程复制
rsync -avz src/ user@host:/path   # 增量同步
ssh user@host               # 远程登录
```

---

## 八、软件包管理

**Debian / Ubuntu（apt）：**
```bash
apt update                 # 更新软件源
apt upgrade                # 升级已安装软件
apt install nginx          # 安装
apt remove nginx           # 卸载（保留配置）
apt purge nginx            # 彻底卸载
apt search keyword         # 搜索
```

**CentOS / RHEL（yum / dnf）：**
```bash
yum install nginx
yum remove nginx
yum update
yum search keyword
```

---

## 九、服务与开机自启（systemd）

```bash
systemctl start nginx        # 启动
systemctl stop nginx         # 停止
systemctl restart nginx      # 重启
systemctl reload nginx       # 重载配置
systemctl status nginx       # 查看状态
systemctl enable nginx       # 设置开机自启
systemctl disable nginx      # 取消开机自启
systemctl list-units --type=service   # 查看所有服务
```

**日志查看：**
```bash
journalctl -u nginx          # 查看某服务日志
journalctl -f                # 实时查看日志
journalctl --since "1 hour ago"
```

---

## 十、文本处理三剑客

```bash
# grep：搜索
grep "error" app.log                 # 查找关键字
grep -r "keyword" /etc/              # 递归搜索
grep -i "error" app.log              # 忽略大小写
grep -n "error" app.log              # 显示行号
grep -v "debug" app.log              # 反向匹配（排除）

# sed：流编辑
sed -i 's/old/new/g' file            # 全局替换
sed -n '10,20p' file                 # 打印 10-20 行
sed -i '5d' file                     # 删除第 5 行

# awk：文本分析
awk '{print $1, $3}' file            # 打印第 1、3 列
awk -F: '{print $1}' /etc/passwd     # 指定分隔符
awk '$3 > 100' data.txt              # 条件过滤
```

**管道与重定向：**
```bash
cmd1 | cmd2                 # 管道，把 cmd1 输出传给 cmd2
cmd > file                  # 覆盖输出到文件
cmd >> file                 # 追加输出到文件
cmd 2> err.log              # 错误输出重定向
cmd > out.log 2>&1          # 标准和错误都输出到文件
cmd < input.txt             # 从文件读取输入
```

---

## 十一、系统信息

```bash
uname -a              # 内核及系统信息
cat /etc/os-release   # 发行版版本
hostname              # 主机名
date                  # 系统时间
timedatectl           # 时区与时间设置
lscpu                 # CPU 信息
lsblk                 # 磁盘分区
fdisk -l              # 磁盘分区详情
mount /dev/sdb1 /mnt  # 挂载
umount /mnt           # 卸载
crontab -e            # 编辑定时任务
crontab -l            # 查看定时任务
```

**crontab 格式：**
```
┌──────────── 分钟 (0-59)
│ ┌────────── 小时 (0-23)
│ │ ┌──────── 日 (1-31)
│ │ │ ┌────── 月 (1-12)
│ │ │ │ ┌──── 星期 (0-7, 0和7都是周日)
* * * * * command
# 例：每天凌晨 2 点执行备份
0 2 * * * /root/backup.sh
```

---

## 十二、实用小技巧

```bash
history                    # 查看历史命令
Ctrl + R                   # 搜索历史命令
Ctrl + C                   # 终止当前命令
Ctrl + Z                   # 暂停任务（fg 恢复）
Ctrl + L                   # 清屏（等同 clear）
!!                         # 重复上一条命令
sudo !!                    # 以管理员身份执行上一条
alias ll='ls -alh'         # 设置命令别名
df -h | sort -k5 -hr       # 磁盘按使用率排序
watch -n 2 df -h           # 每 2 秒刷新一次
```
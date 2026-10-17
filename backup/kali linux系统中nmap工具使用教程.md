![Image](https://github.com/user-attachments/assets/c3f4d961-fee9-4caf-b490-affd28dbd72d)

## kali linux系统中nmap工具使用教程

### **1. 安装与验证**

- 

  安装

  （Kali通常预装）：

  bash

  ```bash
  sudo apt update && sudo apt install nmap
  ```

- 

  验证安装

  ：

  bash

  ```bash
  nmap --version
  ```

------

### **2. 基础扫描**

- 

  扫描单个目标

  ：

  bash

  ```bash
  nmap 192.168.1.1          # IP地址
  nmap example.com          # 域名
  ```

- 

  扫描多个目标

  ：

  bash

  ```bash
  nmap 192.168.1.1 192.168.1.2  # 多个IP
  nmap 192.168.1.1-100          # IP范围
  nmap 192.168.1.0/24           # CIDR子网
  ```

------

### **3. 端口扫描**

- 

  指定端口范围

  ：

  bash

  ```bash
  nmap -p 80 192.168.1.1            # 单端口
  nmap -p 80,443,22 192.168.1.1    # 多个端口
  nmap -p 1-1000 192.168.1.1       # 端口范围
  nmap -p- 192.168.1.1             # 所有端口（1-65535）
  ```

- 

  快速扫描

  （仅常见100端口）：

  bash

  ```bash
  nmap -F 192.168.1.1
  ```

------

### **4. 高级扫描技术**

- 

  服务版本探测

  ：

  bash

  ```bash
  nmap -sV 192.168.1.1
  ```

- 

  操作系统检测

  （需root）：

  bash

  ```bash
  sudo nmap -O 192.168.1.1
  ```

- 

  SYN隐蔽扫描

  （默认，需root）：

  bash

  ```bash
  sudo nmap -sS 192.168.1.1
  ```

- 

  TCP连接扫描

  （无需root）：

  bash

  ```bash
  nmap -sT 192.168.1.1
  ```

- 

  UDP扫描

  （速度较慢）：

  bash

  ```bash
  sudo nmap -sU 192.168.1.1
  ```

------

### **5. 脚本引擎（NSE）**

- 

  使用内置脚本

  ：

  bash

  ```bash
  nmap --script http-title 192.168.1.1        # 单个脚本
  nmap --script vuln 192.168.1.1             # 漏洞检测脚本
  nmap --script "http-*" 192.168.1.1          # 通配符匹配脚本
  ```

- 

  查看脚本帮助

  ：

  bash

  ```bash
  nmap --script-help=http-title
  ```

------

### **6. 输出结果**

- 

  保存结果

  ：

  bash

  ```bash
  nmap -oN result.txt 192.168.1.1          # 普通文本
  nmap -oX result.xml 192.168.1.1          # XML格式
  nmap -oG result.grep 192.168.1.1         # Grep友好格式
  ```

------

### **7. 优化与绕过**

- 

  调整扫描速度

  ：

  bash

  ```bash
  nmap -T4 192.168.1.1    # 更快（T0-T5，默认为T3）
  ```

- 

  跳过主机发现

  （假设主机在线）：

  bash

  ```bash
  nmap -Pn 192.168.1.1
  ```

------

### **8. 组合示例**

bash

```bash
sudo nmap -sS -sV -O -p 1-1000 -T4 --script vuln 192.168.1.1 -oN scan_result.txt
```

- **解释**：SYN扫描 + 服务版本探测 + 操作系统检测 + 1-1000端口 + 漏洞脚本 + 结果保存。

------

### **9. 注意事项**

- **合法合规**：仅在授权的网络中使用，避免非法扫描。
- **权限要求**：SYN扫描（`-sS`）和OS检测（`-O`）需`sudo`权限。
- **扫描速度**：避免过快的扫描（如`-T5`）导致网络或设备过载。

------

通过灵活组合参数，Nmap可适应多种场景，从基础网络探测到深度安全审计。建议通过`man nmap`查阅完整手册。
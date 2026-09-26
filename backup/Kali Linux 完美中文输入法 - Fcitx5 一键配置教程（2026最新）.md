![](https://pic1.imgdb.cn/i/0343whmqwS7Ftylim6SWkJ.webp)

开始之前我推荐飞鸟云VPN机场

200GB流量，不限时，不限速，用完为止，不限设备数量，支持ChatGPT/Claude/Gemini等大模型，支持最新Hysteria2协议，重复购买流量可叠加/10元人民币

还有多种套餐任你选择，点击下方链接获取👇

获取链接：[点击获取👆](https://feiniaoyun.xyz/#/register?code=pl4OIsZ9)

* * *

好的，我们回归正题

在 Kali Linux 上安装中文输入法（通常推荐使用 **Fcitx5** 框架搭配 **Rime** 或 **Pinyin** 引擎，或者 **IBus** 框架），步骤相对标准化。由于 Kali 基于 Debian，主要使用 `apt` 包管理器。

以下是目前最稳定且推荐的 **Fcitx5 + Rime (中州韵)** 或 **Fcitx5 + Pinyin** 的安装配置流程：

### **方法一：使用 Fcitx5 (推荐，性能更好，界面更现代)**

Fcitx5 是新一代输入法框架，比老版的 Fcitx4 更稳定，对 Wayland 和 GTK4/Qt5 支持更好。

#### **1\. 更新软件源并安装核心组件**

打开终端，执行以下命令：

-   `sudo apt update`
    
-   `sudo apt install fcitx5 fcitx5-chinese-addons fcitx5-pinyin fcitx5-rime fcitx5-frontend-gtk3 fcitx5-frontend-gtk4 fcitx5-frontend-qt5 fcitx5-configtool`
    

_注：_`fcitx5-rime` _提供了强大的“中州韵”引擎（支持多种输入方案），_`fcitx5-pinyin` _是基础拼音引擎。建议都装上。_

#### **2\. 设置默认输入法框架**

安装完成后，需要告诉系统使用 Fcitx5：

-   `im-config -n fcitx5`
    

如果提示需要重启或注销，请先记住，稍后操作。

#### **3\. 配置自动启动 (针对桌面环境)**

Kali 默认通常使用 XFCE 桌面。

-   **自动启动设置**：打开菜单 -> 设置 (Settings) -> 会话和启动 (Session and Startup) -> 应用程序自动启动 (Application Autostart)。点击“添加”，名称填 `Fcitx5`，命令填 `fcitx5 -d` (或者在列表中找是否有 Fcitx5 相关选项并勾选)。
    
    或者直接在终端运行以下命令将其加入自启（适用于大多数情况）：
    
    -   `echo "fcitx5 -d" >> ~/.xprofile`
        
    
    _如果是 Wayland 会话，可能需要检查_ `/etc/environment` _或特定的环境变量设置，但在 Kali 默认的 X11 环境下上述方法通常有效。_
    

#### **4\. 添加中文输入法**

1.  重启电脑或注销重新登录（**必须步骤**，否则输入法可能无法加载）。
    
2.  登录后，在应用菜单中找到 **Fcitx5 Configuration** (Fcitx5 配置)。
    
3.  在右侧的 "Available Input Method" (可用输入法) 列表中，取消勾选 "Only Show Current Language" (仅显示当前语言)。
    
4.  搜索 `Pinyin` 或 `Rime`。
    
5.  选中它们，点击中间的箭头按钮添加到左侧的 "Input Method" (已选输入法) 列表中。
    
    -   建议顺序：Keyboard - English, Pinyin, Rime。
        
6.  点击 "Apply" 或 "OK"。
    

#### **5\. 使用与切换**

-   **切换输入法**：默认快捷键通常是 `Ctrl + Space` (控制开关) 或 `Shift` (中英文切换)，也可以在 `Ctrl + Alt + ,` (逗号) 之间循环切换已添加的输入法。
    
-   如果快捷键不生效，可以在 Fcitx5 配置工具的 "Extra" 或 "Global Config" 中查看快捷键设置。
    

* * *

### **方法二：使用 IBus (GNOME 桌面默认，集成度高)**

如果你使用的是 GNOME 桌面或者不想折腾配置文件，IBus 是另一个选择，但通常在 Kali (XFCE) 上 Fcitx5 体验更佳。

1.  **安装 IBus 拼音**：
    
    -   `sudo apt update`
        
    -   `sudo apt install ibus ibus-pinyin`
        
2.  **配置**：
    
    -   运行 `ibus-setup`。
        
    -   在 "Input Method" 标签页点击 "Add"。
        
    -   选择 "Chinese" -> "Pinyin" -> "Add"。
        
3.  **自启**：
    
    -   将 `ibus-daemon -drx` 添加到会话的自动启动项中。
        
4.  **切换**：
    
    -   默认切换键通常是 `Super (Win) + Space`。
        

* * *

### **常见问题排查**

1.  **方框乱码问题**：如果输入中文显示为方框，说明缺少中文字体。安装常用字体：
    
    -   `sudo apt install fonts-wqy-zenhei fonts-wqy-microhei fonts-noto-cjk`
        
    
    安装后可能需要重启浏览器或应用。
    
2.  **在某些程序（如 Firefox, Chrome）中无法输入**：确保安装了前端模块（第一步中已包含 `fcitx5-frontend-gtk3/4` 和 `qt5`）。如果是 Snap 或 Flatpak 版本的应用，可能需要额外的权限配置或环境变量注入，建议优先使用 `apt` 安装的 native 版本浏览器。
    
3.  **Rime 配置**：如果使用 Rime 引擎，首次部署时会在 `~/.local/share/fcitx5/rime/` 生成配置文件。你可以编辑 `default.custom.yaml` 来自定义输入方案（如模糊音、简繁体切换等）。修改后需在托盘图标右键选择 "Deploy" (部署) 生效。
    

**建议**：对于 Kali Linux 用户，**方法一 (Fcitx5 + Pinyin/Rime)** 是最通用且干扰最小的方案。安装完记得**注销并重新登录**。



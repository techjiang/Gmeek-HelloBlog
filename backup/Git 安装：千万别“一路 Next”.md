说实话，每次带新人或者自己换电脑，最折磨人的绝对不是写代码，而是配环境。

网上搜出来的教程，一半是几年前的老黄历，另一半是让你“一路 Next”的敷衍怪。真照着做，后面跑项目的时候绝对报错报到你怀疑人生。今天这篇不整虚的，纯纯的手把手安装实录，把 Git 和 Node/NPM 这两个前端/全栈的“地基”给你打稳，顺便把那些坑爹的雷提前排掉。

---

## 一、Git 安装：千万别“一路 Next”

很多人觉得 Git 就是个压缩包，下个 exe 闭着眼睛点下一步就完事了。结果用的时候中文乱码、换行符报错、SSH 连不上……全是因为安装时那几个不起眼的选项没选对。

### 1. 去哪下载？

**听我一句劝，绝对不要去百度搜索出来的各种“XX软件园”、“XX下载站”下！** 那些大概率是捆绑了流氓软件的假官网。

- **Windows 用户**：直接去官网 `https://git-scm.com/download/win`。如果官网慢得一批，去淘宝镜像或者清华镜像（搜“清华 git 镜像”）下个最新版就行。
- **Mac 用户**：打开终端，直接敲 `xcode-select --install`，弹窗点安装，系统自带的 Git 就装好了，最省事。

### 2. Windows 安装避坑指南（核心！）

双击 exe 开始安装，前面几个协议和路径（建议别放C盘）随便点，**到了下面这几个页面，请停下你狂点 Next 的手**：

- **Select Components（选择组件）**：默认勾选就行，但如果你平时不用 Git GUI（图形界面），可以把 `Git GUI Here` 取消掉，右键菜单干净点。
- **Choosing the default editor（选择默认编辑器）**：**大坑！** 默认选的是 Vim。如果你没受过 Linux 毒打，选 Vim 你连怎么退出都搞不明白。下拉菜单里选 **Use Visual Studio Code as Git's default editor**（前提是你装了 VS Code），或者选你常用的编辑器。
- **Adjusting your PATH environment（配置环境变量）**：**选第二个！** `Git from the command line and also from 3rd-party software`。千万别选第一个（只能在 Git Bash 里用），也别选第三个（会覆盖系统自带的命令，容易搞崩系统）。
- **Configuring the line ending conversions（换行符转换）**：**生死攸关的选项！** 如果你是在 Windows 上开发，选第二个：`Checkout as-is, commit Unix-style line endings`。别选第一个！不然你和用 Mac 的同事协作时，每次提交都会因为换行符（CRLF vs LF）产生一堆无意义的代码冲突，diff 看到你想砸键盘。
- **Choosing a terminal emulator（终端模拟器）**：选 `Use Windows' default console window`。自带的 Git Bash 窗口太丑且不支持很多现代终端快捷键，用 Windows 自带的 PowerShell 或者 Windows Terminal 体验好得多。

后面的一路 Next 到底，安装完成。

### 3. 装完必做的三件事

打开你的命令行（CMD 或 PowerShell），敲以下命令：

```bash
# 1. 告诉 Git 你是谁（不设置这个，你连 commit 都提交不了）
git config --global user.name "你的网名"
git config --global user.email "你的邮箱"

# 2. 解决 Windows 下中文文件名乱码问题（必加！）
git config --global core.quotepath false

# 3. 看看配置成功没
git config --global --list

```

---

## 二、Node.js 与 NPM 安装：别直接去官网下 MSI！

新手最爱干的事：去 Node 官网点个 LTS（长期支持版），下载个 `.msi` 安装包，一路 Next。

**快住手！** 前端圈的生态变化极快，你今天装个 Node 20，明天接手个老项目需要 Node 16，后天跑个新项目需要 Node 22。用官方安装包，切换版本能让你卸载重装到吐。

### 1. 正确姿势：用 nvm 管理 Node（以 Windows 为例）

nvm（Node Version Manager）就是专门用来管理 Node 版本的工具。

- **下载 nvm-windows**：去 GitHub 仓库 `https://github.com/coreybutler/nvm-windows/releases`，下载最新的 `nvm-setup.exe`。
- **安装 nvm**：双击安装。**注意两个路径**：
    1. `nvm` 的安装路径（比如 `D:\nvm`）
    2. `nodejs` 的软链接路径（比如 `D:\nodejs`）

    > ⚠️ **坑点来了**：这两个路径里**绝对不能有中文和空格**！别放在 `D:\我的软件\nvm` 这种地方，后面安装模块会报各种玄学错误。

- **配置 nvm 镜像（提速）**：安装完后，找到 nvm 安装目录下的 `settings.txt` 文件，用记事本打开，在最后面加上这两行：

    ```text
    node_mirror: https://npmmirror.com/mirrors/node/
    npm_mirror: https://npmmirror.com/mirrors/npm/

    ```

    保存。这能让 nvm 下载 Node 的速度起飞。

### 2. 安装 Node 和 NPM

打开一个新的命令行窗口（一定要新的，不然环境变量没刷新），输入：

```bash
# 查看有哪些版本可以装
nvm list available

# 安装一个当前最稳妥的 LTS 版本（比如 20.x）
nvm install 20

# 切换并启用这个版本
nvm use 20

```

这时候，Node 和它自带的 NPM 就同时安装好了。

> **Mac 用户看这里**：Mac 不要用 nvm-windows，去装 `fnm` 或者原版 `nvm`。用 Homebrew 装 fnm 最爽：`brew install fnm`，然后按提示配一下 shell 就行。

### 3. NPM 镜像源配置（别再乱用 cnpm 了！）

NPM 官方源在国内慢得令人发指，但**千万不要去装 cnpm 这个命令行工具**！它的依赖解析逻辑和原生 npm 有细微差别，经常导致“幽灵依赖”问题，项目一上线就炸。

我们只需要把 npm 的下载源换成国内的淘宝新镜像就行了：

```bash
# 设置淘宝新镜像（注意：老域名 taobao.org 早就废了，别用老教程里的！）
npm config set registry https://registry.npmmirror.com

# 验证一下是不是换成功了
npm config get registry

```

### 4. 进阶建议：换用 pnpm

如果你受够了 npm 龟速的安装和庞大的 `node_modules` 文件夹，强烈建议装个 pnpm。它是现在前端圈的主流：

```bash
npm install -g pnpm

```

以后跑项目，把 `npm install` 换成 `pnpm i`，速度能快两三倍，而且极其省硬盘。

---

## 三、怎么验证装好了？以及常见“见鬼”问题

装完别急着关电脑，敲几个命令验证一下：

```bash
git --version    # 应该输出版本号，如 git version 2.4x.x
node -v          # 应该输出 v20.x.x
npm -v           # 应该输出 10.x.x
pnpm -v          # 如果你装了的话

```

### 遇到这些报错怎么办？

| 报错现象 | 原因 | 解决方案 |
| :--- | :--- | :--- |
| `'node' 不是内部或外部命令` | 环境变量没生效，或者 nvm 没切版本 | 关掉命令行重新开；或敲 `nvm use 20`。还不行就去系统环境变量里检查 Path |
| npm install 报 `node-gyp` / `python` 错 | 项目含 C++ 原生模块，缺本地编译环境 | Windows 装 Visual Studio Build Tools（勾选 C++ 桌面开发）；Mac 敲 `xcode-select --install` |
| Mac 下 npm install 报 `EACCES: permission denied` | 权限问题 | **绝对不要加 sudo！** 执行 `sudo chown -R $(whoami) ~/.npm` 修复权限 |
| Git 中文文件名显示 `\346\265\213...` | quotepath 未关闭 | `git config --global core.quotepath false` |
| nvm use 后提示 npm not found | 该 Node 版本下没装 npm | 重新 `nvm install <version>` |

---

## 最后说两句

配环境这事儿，真的是“一次配好，终身受益”。别觉得看这些选项麻烦就凑合，现在省的十分钟，未来可能变成你深夜 debug 时的三个小时。

还有，**永远不要复制粘贴你完全看不懂的命令**，尤其是带 `sudo` 或者 `rm -rf` 的。搞懂它在干嘛，再按回车。

祝各位安装顺利，早点进入“写代码”的正题，别再卡在“配环境”的新手村了！
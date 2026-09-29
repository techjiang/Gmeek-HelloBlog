> **版本**：v1.0　|　**适用对象**：零基础到中高级开发者　|　**阅读方式**：线性阅读 + 按需查阅附录
>
> **说明**：Git 命令行为稳定，本文所有命令均长期有效；GitHub 网页界面（按钮位置、菜单名称）会随产品迭代变化，涉及界面操作处请以 [GitHub 官方文档](https://docs.github.com) 最新说明为准。文中所有示例均可在本地沙箱环境中安全复现。

---

## 目录

- [1. 概念基础](#1-概念基础)
- [2. 环境准备与账号配置](#2-环境准备与账号配置)
- [3. 本地仓库核心操作](#3-本地仓库核心操作)
- [4. 远程仓库与多人协作](#4-远程仓库与多人协作)
- [5. 分支模型与集成策略](#5-分支模型与集成策略)
- [6. GitHub 平台协作实战](#6-github-平台协作实战)
- [7. 自动化：GitHub Actions](#7-自动化github-actions)
- [8. 文档、站点与项目管理](#8-文档站点与项目管理)
- [9. 安全、合规与质量保障](#9-安全合规与质量保障)
- [10. 进阶命令与高级技巧](#10-进阶命令与高级技巧)
- [11. 疑难排错手册](#11-疑难排错手册)
- [12. 工程化最佳实践](#12-工程化最佳实践)
- [附录 A. 命令速查表](#附录-a-命令速查表)
- [附录 B. 术语表](#附录-b-术语表)
- [附录 C. 延伸学习资源](#附录-c-延伸学习资源)

---

## 1. 概念基础

### 1.1 Git 与 GitHub 的区别

这是初学者最常见的混淆点，必须先厘清：

| 维度 | Git | GitHub |
| --- | --- | --- |
| 本质 | 版本控制**软件**，运行在你的机器上 | 托管 Git 仓库的**云平台**（SaaS） |
| 依赖关系 | 不依赖任何网络或平台 | 依赖 Git，是 Git 生态的上层服务 |
| 断网可用 | 完全可用 | 不可用 |
| 同类产品 | SVN、Mercurial、Perforce | GitLab、Bitbucket、Gitee、Coding |

一句话记忆：**Git 是引擎，GitHub 是修在引擎之上的高速公路、服务区和车友会。** 你完全可以在不上 GitHub 的情况下精通 Git；但要参与现代开源协作，两者都需要掌握。

### 1.2 分布式版本控制的核心思想

集中式版本控制（如 SVN）的每一次提交都要与中央服务器通信；**分布式**版本控制下，每个开发者本地都拥有仓库的**完整副本**，包括全部历史。

这带来三个直接收益：

1. **离线工作**：提交、查看历史、创建分支均不联网。
2. **天然备份**：任何一台机器上的副本都能完整恢复项目。
3. **廉价分支**：分支只是对某个提交对象的可移动指针，创建成本几乎为零。

代价是需要理解更多的概念模型——而 Git 的概念模型一旦打通，剩下的只是命令记忆。

### 1.3 Git 的三区模型与文件状态

理解 Git 的全部日常操作，只需要掌握一个模型：

```
工作区(Working Directory)  --git add-->  暂存区(Staging Area / Index)  --git commit-->  本地仓库(Repository)
```

文件在任一时刻处于四种状态之一：

| 状态 | 含义 | 常用查看命令 |
| --- | --- | --- |
| `untracked` | 新文件，Git 尚未跟踪 | `git status` |
| `modified` | 已跟踪文件被改动，未暂存 | `git diff` |
| `staged` | 改动已放入暂存区，待提交 | `git diff --staged` |
| `committed` | 已写入仓库，形成快照 | `git log` |

**关键心法**：`git commit` 提交的是**暂存区的内容**，而不是工作区的当前状态。很多「我明明改了代码为什么没提交上去」的问题都源于此。

### 1.4 仓库里到底有什么：.git 目录

执行 `git init` 后，当前目录下会生成一个隐藏的 `.git` 文件夹。它是仓库的全部：对象数据库、引用（分支与标签的指针）、配置、暂存区快照、钩子脚本等。

- **删掉 `.git`** = 删掉全部版本历史（只剩当前工作区文件）。
- **拷贝 `.git` 到别处** = 完整复制整个仓库。

因此，`git clone` 实际上就是「拷贝对象数据库 + 检出工作区文件」。

### 1.5 提交对象与 SHA：为什么 Git 快而可靠

每次 `git commit` 都会产生一个**提交对象（commit object）**，包含：

- 项目根的**树对象（tree）** 指针（即完整快照）；
- 一个或多个**父提交**指针；
- 作者、提交者、时间戳；
- 提交信息。

这四者做哈希后得到一个 40 位 SHA-1/SHA-256 标识（如 `a1b2c3d...`）。

三个推论值得记住：

1. **历史不可篡改**：改一个字符，哈希全变，后续所有提交的哈希都会连锁改变。
2. **内容寻址**：内容相同的文件只存一份，这也是 Git 远比想象中节省空间的原因。
3. **引用是指针**：`main`、`feature-x`、`v1.0.0` 本质上都是「指向某个提交的书签」，移动书签的成本是 O(1)。

### 1.6 常见误解澄清

| 误解 | 事实 |
| --- | --- |
| 「Git 存的是文件差异」 | Git 存的是**完整快照**，未修改的文件用指针复用旧对象 |
| 「`git pull` 就是下载代码」 | `pull` = `fetch` + 合并（merge 或 rebase），会产生新提交 |
| 「删掉分支就删掉了代码」 | 只删除了指针，提交对象仍在，短期内可用 `git reflog` 找回 |
| 「commit 是推给 GitHub」 | `commit` 只写本地，必须 `push` 才会上传 |
| 「GitHub = 代码网盘」 | GitHub 的价值在于围绕 Git 的协作流、自动化与生态 |

---

## 2. 环境准备与账号配置

### 2.1 安装 Git

| 平台 | 方式 |
| --- | --- |
| Windows | 从 [git-scm.com/download/win](https://git-scm.com/download/win) 下载安装包；安装时建议选择 *Add Git to PATH*、使用 VS Code 作为默认编辑器 |
| macOS | `xcode-select --install`，或 `brew install git` |
| Linux (Debian/Ubuntu) | `sudo apt update && sudo apt install git` |
| Linux (RHEL/Fedora) | `sudo dnf install git` |

安装后验证：

```bash
git --version
# 期望输出类似：git version 2.45.2
```

### 2.2 首次全局配置

```bash
# 1. 身份（会写入每一次提交的元数据，务必与 GitHub 账号邮箱一致）
git config --global user.name  "你的名字"
git config --global user.email "you@example.com"

# 2. 默认分支名（现代惯例为 main）
git config --global init.defaultBranch main

# 3. 默认编辑器（二选一）
git config --global core.editor "code --wait"      # VS Code
git config --global core.editor "vim"              # Vim

# 4. 换行符处理（跨平台团队必配）
# Windows:
git config --global core.autocrlf true
# macOS / Linux:
git config --global core.autocrlf input

# 5. 输出美化
git config --global color.ui auto
git config --global pull.rebase false   # 或 true，见 5.5 节

# 6. 常用别名（强烈推荐）
git config --global alias.st  "status -sb"
git config --global alias.co  "checkout"
git config --global alias.br  "branch"
git config --global alias.ci  "commit"
git config --global alias.lg  "log --oneline --graph --decorate --all"
```

查看与检查配置：

```bash
git config --list --show-origin     # 列出全部配置及其来源文件
git config --global --get user.email
```

配置文件位置：`~/.gitconfig`（全局）、`.git/config`（仓库级）、`/etc/gitconfig`（系统级）。优先级：**仓库 > 全局 > 系统**。

### 2.3 选择连接方式：SSH 还是 HTTPS

| 维度 | SSH | HTTPS + Token |
| --- | --- | --- |
| 首次配置 | 略繁琐（需生成密钥） | 简单 |
| 日常体验 | 免密、快 | 需凭据管理器缓存 |
| 企业防火墙 | 可能被封 22 端口 | 走 443，兼容性好 |
| 适用场景 | 个人长期开发 | 受限网络 / CI |

> **重要事实**：自 2021 年 8 月起，GitHub **不再接受账号密码**作为 Git 操作的认证方式，必须使用 SSH 密钥或 Personal Access Token（PAT）。

### 2.4 生成并添加 SSH 密钥

```bash
# 1. 生成密钥（推荐 Ed25519，更安全更短）
ssh-keygen -t ed25519 -C "you@example.com"
# 一路回车即可；如需设置 passphrase，务必记住

# 2. 启动 ssh-agent 并加载私钥
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 3. 复制公钥内容
# macOS:
pbcopy < ~/.ssh/id_ed25519.pub
# Linux:
xclip -selection clipboard < ~/.ssh/id_ed25519.pub
# Windows (PowerShell):
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard
```

然后在 GitHub 网页端：**Settings → SSH and GPG keys → New SSH key**，粘贴公钥并保存。最后测试：

```bash
ssh -T git@github.com
# 成功输出：Hi <username>! You've successfully authenticated...
```

### 2.5 Personal Access Token（PAT）

适用于 HTTPS 方式。在 **Settings → Developer settings** 中创建：

- **Fine-grained token**（推荐）：可限定仓库范围与具体权限（读/写）。
- **Classic token**：可选 `repo`、`workflow`、`read:org` 等粗粒度 scope。

安全守则：

1. 设置**最小必要权限**与**合理有效期**。
2. 绝不提交到代码仓库，绝不通过聊天工具明文传输。
3. 泄露立即撤销并重新生成。
4. CI 中使用 GitHub Actions 的 `secrets`，不要硬编码。

Windows 与 macOS 的 Git 通常自带凭据管理器（Git Credential Manager），`git push` 一次后会安全缓存凭据。Linux 可用 `libsecret` 帮手或改用 SSH。

### 2.6 多账号配置

同一台机器同时使用公司账号与个人账号时，用 `~/.ssh/config` 分流：

```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes

Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
```

个人仓库正常克隆；公司仓库使用 `git clone git@github-work:company/repo.git`，再单独设置仓库级身份：

```bash
git config user.name  "张三（工作）"
git config user.email "zhangsan@company.com"
```

### 2.7 验证环境

```bash
git config --get user.name
git config --get user.email
ssh -T git@github.com          # SSH 方式
git ls-remote origin           # 能列出远程引用即为连通
```

---

## 3. 本地仓库核心操作

### 3.1 创建仓库的两种方式

```bash
# 方式 A：本地新建，之后再关联远程
mkdir my-project && cd my-project
git init

# 方式 B：克隆已有仓库
git clone git@github.com:user/repo.git
cd repo
```

`git clone` 会自动完成三件事：下载全部历史、检出默认分支、配置 `origin` 远程并建立上游跟踪。

### 3.2 第一次提交完整流程

```bash
# 1. 创建文件
echo "# My Project" > README.md

# 2. 查看状态（养成随时 status 的习惯）
git status

# 3. 暂存
git add README.md

# 4. 提交（-m 直接写信息）
git commit -m "docs: 初始化项目 README"

# 5. 关联远程并推送
git remote add origin git@github.com:user/my-project.git
git push -u origin main
```

> `-u`（即 `--set-upstream`）建立本地分支与远程分支的跟踪关系，此后只需 `git push` / `git pull`。

### 3.3 查看状态与历史

```bash
git status                      # 完整状态
git status -sb                  # 简洁版 + 与上游差异
git diff                        # 工作区 vs 暂存区
git diff --staged               # 暂存区 vs 上一次提交
git diff main..feature-x        # 两个分支差异

git log                         # 完整历史
git log --oneline --graph --decorate --all   # 图形化历史
git log -5                      # 最近 5 条
git log --author="张三"          # 按作者
git log -- path/to/file.py      # 单文件历史
git log --stat                  # 附带改动统计
git show <commit-sha>           # 查看某次提交详情
git shortlog -sn                # 贡献者提交统计
git blame path/to/file.py       # 逐行追溯责任人
```

### 3.4 暂存区的艺术

```bash
git add <file>                  # 暂存整个文件
git add .                       # 暂存当前目录全部改动（谨慎）
git add -p                      # 交互式逐块暂存（强烈推荐）
git add -A                      # 暂存全部，含新增与删除
git restore --staged <file>     # 取消暂存（文件改动保留在工作区）
git rm --cached <file>          # 停止跟踪但保留本地文件
git rm <file>                   # 删除文件并暂存删除操作
```

**`git add -p` 是区分新手与熟手的分水岭**：它让你把一个文件里的多个逻辑改动拆分到不同提交中，让提交历史真正可读。

### 3.5 撤销与修改：五种场景对照

| 场景 | 命令 |
| --- | --- |
| 改错了工作区文件，想恢复 | `git restore <file>` |
| 暂存错了，想退回工作区 | `git restore --staged <file>` |
| 提交信息写错了（未 push） | `git commit --amend -m "新信息"` |
| 漏了文件（未 push） | `git add <file> && git commit --amend --no-edit` |
| 已 push 的提交需要抵消 | `git revert <sha>`（生成反向提交，安全） |
| 未 push，想丢弃最近 N 个提交 | `git reset --hard HEAD~N`（危险） |

```bash
# revert：历史友好，适合共享分支
git revert abc1234

# reset：改写历史，仅限本地未推送分支
git reset --soft  HEAD~1   # 保留暂存区与工作区（最温和）
git reset --mixed HEAD~1   # 保留工作区（默认）
git reset --hard  HEAD~1   # 全部丢弃（最危险）
```

### 3.6 .gitignore

`.gitignore` 只对**尚未被跟踪**的文件生效。已经被 Git 跟踪的文件不会因为加入 `.gitignore` 而被忽略。

典型模板：

```gitignore
# 依赖
node_modules/
vendor/
.venv/
__pycache__/

# 构建产物
dist/
build/
*.log
*.tmp

# 环境与密钥（务必忽略）
.env
.env.*
!.env.example
*.pem
*.key

# 系统与编辑器
.DS_Store
Thumbs.db
.idea/
.vscode/*
!.vscode/settings.json
!.vscode/extensions.json

# 覆盖规则示例：忽略所有 .md，但保留 README.md
*.md
!README.md
```

调试忽略规则的利器：

```bash
git check-ignore -v config/local.env   # 显示是哪条规则命中了该文件
```

**团队必做**：项目第一天就提交 `.gitignore`，并通过 `.gitignore` 之外的 `.gitattributes` 固定换行符与二进制标记（见 11.3 节）。

### 3.7 提交信息规范

推荐采用 **Conventional Commits**（约定式提交）：

```
<type>(<scope>): <简短描述>

<正文（可选，解释动机与背景）>

<页脚（可选，BREAKING CHANGE / 关闭的 Issue）>
```

常用 `type`：

| type | 含义 |
| --- | --- |
| `feat` | 新功能 |
| `fix` | 缺陷修复 |
| `docs` | 文档 |
| `style` | 格式（不影响语义） |
| `refactor` | 重构（非新功能、非修 bug） |
| `perf` | 性能优化 |
| `test` | 测试 |
| `build` | 构建系统或外部依赖 |
| `ci` | CI 配置 |
| `chore` | 杂务 |

示例：

```
feat(auth): 支持邮箱验证码登录

用户反馈密码登录流程过长，新增 6 位验证码登录入口，
验证码有效期 5 分钟，连续错误 5 次后锁定 10 分钟。

Closes #128
```

写好提交信息的三条铁律：

1. **首行 ≤ 72 字符**，用祈使语气（"add" 而非 "added" / "adds"）。
2. **正文解释 why，而不是复述 what**（what 已经在 diff 里了）。
3. **一次提交只做一件事**。做不到就用 `git add -p` 拆分。

---

## 4. 远程仓库与多人协作

### 4.1 远程管理

```bash
git remote -v                      # 查看远程
git remote add upstream git@github.com:original/repo.git   # 添加上游
git remote rename origin fork
git remote remove upstream
git remote show origin             # 查看远程详细信息
git fetch --prune                  # 清理本地已失效的远程跟踪分支
```

### 4.2 push / fetch / pull 的区别

| 命令 | 网络 | 是否改工作区 | 是否产生提交 | 安全性 |
| --- | --- | --- | --- | --- |
| `git fetch` | 下载 | 否 | 否 | 最安全，只更新远程跟踪分支 |
| `git pull` | 下载 + 集成 | 可能改动 | 可能产生合并提交 | 可能引发冲突 |
| `git push` | 上传 | 否 | 否 | 被拒时需先同步 |

```bash
git fetch origin                # 拉取但不合并
git log main..origin/main       # 先看看远程多了什么
git pull --rebase origin main   # 决定合并策略后再集成
```

**推荐习惯**：不确定远程发生了什么时，先 `fetch` + 查看，再决定 `merge` 还是 `rebase`。

### 4.3 强制推送

```bash
git push --force-with-lease     # 相对安全：仅当远程未被他人更新时才覆盖
git push --force                # 无条件覆盖，慎用
```

`--force-with-lease` 是 `--force` 的安全替代品，它会检查远程分支是否仍是本地记录的那个状态，若他人已推送则拒绝执行。**在共享分支上永远不要使用任何形式的强制推送。**

### 4.4 多人协作的两种形态

**形态 A：共享仓库（企业内部常见）**
所有人有写权限，直接向主仓库推送分支并发起 PR。

**形态 B：Fork 工作流（开源项目常见）**
贡献者无主仓库写权限，流程为：

```bash
# 1. Fork 后克隆自己的副本
git clone git@github.com:your-name/repo.git
cd repo

# 2. 关联上游
git remote add upstream git@github.com:original/repo.git

# 3. 保持与上游同步
git fetch upstream
git checkout main
git merge upstream/main          # 或 rebase
git push origin main

# 4. 开发
git checkout -b feature/my-change
# ...修改、add、commit...
git push -u origin feature/my-change

# 5. 在 GitHub 上向 upstream 发起 Pull Request
```

### 4.5 避免常见的同步冲突

- 每天开工先 `git pull --rebase`，收工前 `git push`。
- 长期分支定期同步主干，避免最后一次性合并产生「冲突海啸」。
- 合并前用 `git fetch` + `git diff main...feature` 自查改动范围。

---

## 5. 分支模型与集成策略

### 5.1 为什么需要分支

分支让「并行开发」成为可能：功能开发、缺陷修复、实验性重构互不干扰。在 Git 中创建分支只是写入一个 41 字节左右的引用文件，因此**应当频繁建分支、及时合并、及时删除**。

### 5.2 分支基础命令

```bash
git branch                        # 列出本地分支
git branch -a                     # 含远程分支
git branch --show-current         # 当前分支名
git branch feature/login          # 创建分支（不切换）
git switch -c feature/login       # 创建并切换（推荐）
git switch main                   # 切换
git branch -m new-name            # 重命名当前分支
git branch -d feature/login       # 删除已合并分支
git branch -D feature/login       # 强制删除未合并分支
git push origin --delete feature/login   # 删除远程分支
```

> `git switch` 与 `git restore` 是 Git 2.23 引入的语义化命令，用于取代职责不清的 `git checkout`。新项目建议直接使用新命令。

### 5.3 合并：fast-forward 与三方合并

```bash
git switch main
git merge feature/login
git merge --no-ff feature/login   # 强制保留合并节点
git merge --squash feature/login  # 压缩为单个提交（需手动 commit）
```

- **fast-forward（快进）**：目标分支没有新提交时，`main` 指针直接前移，不产生合并提交。
- **三方合并**：两边都有新提交时，Git 找到共同祖先，生成一个有两个父提交的合并节点。

**选择建议**：功能分支保留完整历史用 `--no-ff`；希望主干历史线性、单个功能只有一个节点用 `--squash`。

### 5.4 冲突解决标准流程

冲突发生在 Git 无法自动决定取舍时：

```
<<<<<<< HEAD
你的代码（当前分支）
=======
对方的代码（被合并分支）
>>>>>>> feature/other
```

标准处置流程：

```bash
# 1. 触发冲突后，status 会列出 both modified
git status

# 2. 手动编辑文件，删除冲突标记，决定最终内容
#    建议用 IDE 的冲突解决面板（VS Code 提供 Accept Current/Incoming/Both）

# 3. 标记为已解决
git add <file>

# 4. 继续合并 / 变基
git merge --continue        # 或 git merge（早期版本）
git rebase --continue

# 5. 如需中止，回到冲突前状态
git merge --abort
git rebase --abort
```

冲突预防：

- 小步提交、频繁同步，缩短分支生命周期。
- 团队约定代码格式化工具（Prettier / Black / gofmt），减少纯格式冲突。
- 大规模重构单独开分支并**快速合并**，不与其他功能混行。

### 5.5 rebase 的正确打开方式

```bash
git switch feature/login
git rebase main                 # 把 feature 的提交搬到 main 最新提交之上
git rebase -i HEAD~3            # 交互式整理最近 3 个提交
git push --force-with-lease     # 变基后需强制推送自己的分支
```

交互式 rebase 可对提交执行 `pick` / `reword` / `squash` / `fixup` / `drop` / `edit`，是打造整洁历史的核心工具。

> **黄金法则：绝不 rebase 已经推送给他人并被他人基于其工作的提交。** 变基会重写提交哈希，对他人而言是「凭空消失的历史」。

### 5.6 merge 还是 rebase

| 维度 | merge | rebase |
| --- | --- | --- |
| 历史形态 | 有分叉、有合并节点 | 线性 |
| 可追溯性 | 完整保留真实协作轨迹 | 改写历史 |
| 冲突处理 | 一次性解决 | 可能逐个提交解决 |
| 适用场景 | 共享分支、release 集成 | 个人分支同步主干、整理提交 |

**推荐策略**：个人分支同步主干用 `rebase`；功能分支合入主干用 `merge`（或 squash merge）。

### 5.7 常见分支模型

**GitHub Flow（轻量，适合持续部署）**
仅 `main` + 短命功能分支：`branch → commit → PR → review → merge → deploy`。规则：`main` 始终可发布；一切改动走 PR。

**Git Flow（适合有明确版本发布的软件）**
长期分支：`main`（发布）、`develop`（集成）；辅助分支：`feature/*`、`release/*`、`hotfix/*`。流程严谨但较重，Web 项目通常不必采用。

**Trunk-Based（主干开发）**
所有人直接向 `main` 提交（或用极短分支，< 1 天），配合特性开关（feature flag）与完善的 CI。适合高频发布、自动化成熟的团队。

**选择建议**：多数团队用 **GitHub Flow** 起步，在发布节奏变复杂时再引入 Git Flow 元素。

### 5.8 标签与版本号

采用 **语义化版本（SemVer）**：`主版本.次版本.修订号`（MAJOR.MINOR.PATCH）

- **PATCH**：向后兼容的缺陷修复。
- **MINOR**：向后兼容的新功能。
- **MAJOR**：不兼容的 API 变更。

```bash
git tag                         # 列出标签
git tag v1.2.0                  # 轻量标签
git tag -a v1.2.0 -m "发布 1.2.0：新增导出功能"   # 附注标签（推荐）
git show v1.2.0
git push origin v1.2.0          # 推送单个标签
git push origin --tags          # 推送全部标签
git tag -d v1.2.0               # 删除本地标签
git push origin --delete v1.2.0 # 删除远程标签
```

---

## 6. GitHub 平台协作实战

### 6.1 创建仓库与初始化

新建仓库时的决策清单：

| 项目 | 建议 |
| --- | --- |
| 可见性 | 开源选 Public；含业务逻辑/密钥一律 Private |
| README | 项目第一天就写，哪怕只有三行 |
| .gitignore | 选择对应语言模板 |
| LICENSE | 开源项目**必须**选择（见 9.1） |
| 描述与 Topics | 3–5 个主题标签，提升可发现性 |

README 推荐结构：

```markdown
# 项目名

> 一句话说明这个项目解决什么问题。

## 特性
## 快速开始
## 使用示例
## 配置项
## 部署
## 贡献指南
## 许可证
```

### 6.2 Issue 的写法与标签体系

一个高质量 Issue 应包含：**标题（what）+ 环境信息 + 复现步骤 + 期望行为 + 实际行为 + 日志/截图 + 可能的解决方案**。

推荐标签体系：

| 类别 | 示例 |
| --- | --- |
| 类型 | `bug`、`feature`、`docs`、`question`、`chore` |
| 优先级 | `P0`、`P1`、`P2` |
| 状态 | `good first issue`、`help wanted`、`wontfix`、`duplicate` |
| 模块 | `ui`、`api`、`infra`、`perf` |

`good first issue` 与 `help wanted` 是 GitHub 官方推荐的新人友好标签，能显著提升开源项目的参与度。

### 6.3 Pull Request 完整流程

1. **创建分支**：从最新的 `main` 切出 `feature/xxx` 或 `fix/issue-128`。
2. **开发与提交**：小步、规范提交信息。
3. **自检**：跑测试、跑 lint、确认无调试代码与敏感信息。
4. **推送并发起 PR**：`git push -u origin feature/xxx`，然后在网页端创建 PR。
5. **填写 PR 描述**：动机、改动点、影响范围、如何验证、关联 Issue（`Closes #128`）。
6. **代码评审**：回应每条评论，用 `git commit --fixup` 或追加提交修复。
7. **CI 通过 + 评审人批准**后合并。
8. **清理**：删除功能分支，必要时同步主干。

PR 标题同样遵循约定式提交规范，这样合并后主干历史自动整洁。

### 6.4 Code Review 礼仪

**给评审人**：

- 24 小时内给出首次反馈（哪怕是「我今天晚些看」）。
- 区分**阻塞问题**（must）与**建议**（nit / suggestion）。
- 用提问句代替命令句：「这里是否考虑过 X 场景？」
- 单次评审代码量控制在 400 行以内，超过则建议拆分。

**给提交人**：

- PR 尽量小（< 400 行变更），一个 PR 只做一件事。
- 自己先审一遍 diff 再请求评审。
- 对每条评论要么修改、要么解释，不要已读不回。
- 采纳建议后用 👍 等简短回应，避免无意义的「done」串。

### 6.5 用 Pull Request 参与开源

```bash
# Fork → 克隆 → 建分支 → 修改 → 提交 → 推送 → 发起 PR
git clone git@github.com:your-name/project.git
cd project
git remote add upstream git@github.com:org/project.git
git switch -c fix/typo-in-docs
# ...编辑...
git add docs/README.md
git commit -docs: 修正文档中的安装命令拼写错误"
git push -u origin fix/typo-in-docs
```

然后在自己仓库页面点击 **Contribute → Open pull request**。首次贡献前请务必阅读仓库根目录的 `CONTRIBUTING.md`。

### 6.6 Release 与发布

```bash
git switch main
git tag -a v2.0.0 -m "v2.0.0"
git push origin v2.0.0
```

在 GitHub 上通过 **Releases → Draft a new release** 选择该标签，撰写发布说明（可自动生成变更列表），并上传编译产物。启用「自动生成发布说明」可节省大量时间。

### 6.7 项目管理：Projects

GitHub Projects（新版）是内建的看板/表格工具，支持：

- 自定义字段（优先级、迭代、负责人、预估工时）。
- 自动化（PR 合并后自动关闭卡片、Issue 创建后自动入列）。
- 与 Issues / PR 双向关联。

小型团队完全可以替代外部项目管理工具，做到「代码与计划在同一处」。

### 6.8 协作自动化模板

**Issue 模板**（`.github/ISSUE_TEMPLATE/bug_report.md`）：

```markdown
---
name: 缺陷反馈
about: 报告一个可复现的问题
title: "[bug] "
labels: bug
assignees: ""
---

## 环境
- 操作系统：
- 版本号：
- 运行环境：

## 复现步骤
1.
2.
3.

## 期望行为
## 实际行为
## 日志 / 截图
```

**PR 模板**（`.github/pull_request_template.md`）：

```markdown
## 改动说明
## 关联 Issue
Closes #
## 影响范围
## 自测清单
- [ ] 单元测试通过
- [ ] 本地验证通过
- [ ] 无调试代码 / 敏感信息
- [ ] 文档已同步更新
```

**代码所有者**（`.github/CODEOWNERS`）：

```
# 语法：路径  负责人
*                 @org/core-team
/docs/            @org/tech-writers
/src/api/         @org/backend-team
*.sql             @org/dba
```

配置后，涉及对应路径的 PR 会自动请求相应评审人，可配合分支保护强制要求其批准。

### 6.9 GitHub CLI：`gh`

官方命令行工具，把网页操作搬进终端：

```bash
gh auth login                          # 登录
gh repo create my-project --public     # 创建仓库
gh issue list --label bug              # 列出 Issue
gh issue create --title "..." --body "..."
gh pr create --fill                    # 用提交信息自动填充 PR
gh pr status                           # 查看 PR 状态
gh pr checkout 128                     # 检出某个 PR 分支
gh pr merge 128 --squash --delete-branch
gh run list                            # 查看 Actions 运行记录
gh run watch                           # 实时跟踪运行
gh release create v1.0.0 ./dist/*.zip  # 创建 Release 并上传产物
```

---

## 7. 自动化：GitHub Actions

### 7.1 核心概念

| 概念 | 说明 |
| --- | --- |
| Workflow（工作流） | `.github/workflows/*.yml` 中定义的自动化流程 |
| Event（事件） | 触发条件，如 `push`、`pull_request`、`schedule` |
| Job（作业） | 一个运行环境上的一组步骤，多个 Job 默认并行 |
| Step（步骤） | 作业内的单条指令，可为 shell 命令或预置 Action |
| Action（动作） | 可复用的自动化单元，支持官方、社区或自研 |
| Runner（运行器） | 执行作业的机器（GitHub 托管或自托管） |

### 7.2 实战：Node.js 项目 CI

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

permissions:
  contents: read

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        node-version: [18.x, 20.x, 22.x]
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint --if-present

      - name: Test
        run: npm test --if-present

      - name: Build
        run: npm run build --if-present
```

要点解读：

- `permissions: contents: read` 落实**最小权限**原则。
- `concurrency` 避免同一分支的多次推送重复占用构建资源。
- `matrix` 让同一份流程在多个 Node 版本上并行验证。
- `npm ci` 相比 `npm install` 严格按 lockfile 安装，更适合 CI。

> **版本提示**：`actions/checkout@v4`、`actions/setup-node@v4` 等版本号会持续迭代，采用前请到 GitHub Marketplace 核对最新主版本号。

### 7.3 常用触发器

```yaml
on:
  push:
    branches: [ main, "release/**" ]
    tags: [ "v*" ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: "0 2 * * 1"     # 每周一 UTC 02:00
  workflow_dispatch:          # 手动触发
  issues:
    types: [ opened, labeled ]
```

### 7.4 Secrets 与环境变量

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Use secret
        env:
          API_KEY: ${{ secrets.API_KEY }}
        run: ./deploy.sh
```

安全要点：

- 敏感值一律放 **Settings → Secrets and variables → Actions**，分 `Repository` 与 `Environment` 两级。
- 不要在 `run` 中 `echo` 密钥（GitHub 会自动脱敏，但不要依赖它）。
- PR 来自 Fork 时**无法读取**仓库 Secrets，这是刻意的安全设计。

### 7.5 缓存与性能

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: npm              # 内建缓存
```

或手动缓存：

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-npm-
```

### 7.6 部署到 GitHub Pages

```yaml
# .github/workflows/deploy-pages.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

启用路径：仓库 **Settings → Pages → Source** 选择 **GitHub Actions**。

### 7.7 常用工作流清单

| 场景 | 作用 |
| --- | --- |
| Lint + Test | 每个 PR 自动化质量门禁 |
| 自动发布 | 打 tag 后自动构建并创建 Release |
| 依赖升级 | 定时构建以提前发现依赖破坏性变更 |
| Issue/PR 分类 | 依据路径自动打标签、指派负责人 |
| 文档部署 | 文档变更自动构建并发布站点 |
| 镜像构建 | 构建并推送容器镜像至镜像仓库 |
| 定时报告 | 每日生成未关闭 Issue 摘要 |

---

## 8. 文档、站点与项目管理

### 8.1 文档体系

成熟的仓库通常包含：

| 文件 | 作用 |
| --- | --- |
| `README.md` | 项目门面，5 分钟内让人跑起来 |
| `CONTRIBUTING.md` | 贡献流程、开发环境、提交规范 |
| `CHANGELOG.md` | 按版本记录变更，可配合自动工具生成 |
| `CODE_OF_CONDUCT.md` | 社区行为准则 |
| `SECURITY.md` | 漏洞上报渠道与响应承诺 |
| `LICENSE` | 法律许可文本 |
| `docs/` | 深度文档、架构设计、API 说明 |

`CHANGELOG` 推荐按 **Keep a Changelog** 结构组织：`Added / Changed / Deprecated / Removed / Fixed / Security`。

### 8.2 GitHub Pages

三种常见形态：

1. **文档站**：使用 MkDocs / Docusaurus / VitePress 构建，配合 Actions 自动部署。
2. **项目主页**：仓库根目录放 `index.html`，直接发布。
3. **个人主页**：创建同名仓库 `<username>.github.io`。

自定义域名时，需在仓库根目录放置 `CNAME` 文件，并在域名服务商处配置 DNS（`A` 记录指向 GitHub Pages 的 IP，或 `CNAME` 指向 `<username>.github.io`）。

### 8.3 Discussions 与 Wiki

- **Discussions**：适合问答、想法征集、Show & Tell，减轻 Issue 列表噪音。
- **Wiki**：适合长篇、需要版本化的协作式文档；缺点是与代码不在同一提交历史中。小型项目优先使用 `docs/` 目录。

### 8.4 Codespaces 与 devcontainer

Codespaces 提供云端开发环境，配套 `.devcontainer/devcontainer.json` 可固化开发环境：

```json
{
  "name": "Node.js Dev",
  "image": "mcr.microsoft.com/devcontainers/javascript-node:20",
  "postCreateCommand": "npm install",
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode"
      ]
    }
  }
}
```

收益：新人「一键进入开发」，环境不一致导致的「在我机器上是好的」问题显著减少。

---

## 9. 安全、合规与质量保障

### 9.1 许可证选择

没有 `LICENSE` 文件的公开仓库，法律上默认「保留所有权利」，他人无权使用。

| 许可证 | 特点 | 适用 |
| --- | --- | --- |
| MIT | 极简、宽松、几乎无限制 | 大多数工具库 |
| Apache-2.0 | 宽松 + 明确专利授权 + 商标条款 | 企业级开源 |
| BSD-3-Clause | 宽松 + 禁止用作者名义宣传 | 学术与基础库 |
| MPL-2.0 | 文件级弱著佐权 | 想保持衍生开源的项目 |
| GPL-3.0 | 强著佐权，衍生作品须开源 | 希望保持自由的软件 |
| AGPL-3.0 | 著佐权 + 网络服务也须开源 | SaaS 场景 |
| LGPL-3.0 | 库级别著佐权，可被闭源调用 | 开源基础库 |

**快速建议**：个人工具用 MIT；带专利风险的大型项目用 Apache-2.0；坚持开源传播用 GPL/AGPL。团队采用前请咨询法务。

### 9.2 依赖安全

**Dependabot**（`.github/dependabot.yml`）：

```yaml
version: 2
updates:
  - package-ecosystem: npm
    directory: "/"
    schedule:
      interval: weekly
    open-pull-requests-limit: 10

  - package-ecosystem: github-actions
    directory: "/"
    schedule:
      interval: monthly

  - package-ecosystem: docker
    directory: "/"
    schedule:
      interval: weekly
```

配合仓库 **Settings → Code security** 开启：

- **Dependency graph**（依赖图谱）
- **Dependabot alerts**（漏洞告警）
- **Dependabot security updates**（自动安全升级）
- **Secret scanning**（密钥扫描）
- **Push protection**（推送前拦截密钥，强烈建议开启）

### 9.3 敏感信息泄露的处置

**假设最坏情况：你已经把密钥提交并推送了。**

```bash
# 1. 立即到服务提供方撤销 / 轮换该密钥（这是第一步，也是最重要的一步）
# 2. 从当前代码中移除
git rm --cached config/secrets.json
echo "config/secrets.json" >> .gitignore
git add .gitignore
git commit -m "chore: 移除误提交的敏感配置"

# 3. 如需彻底抹除历史（谨慎！会改写所有相关提交哈希）
pip install git-filter-repo
git filter-repo --invert-paths --path config/secrets.json
git remote add origin git@github.com:user/repo.git
git push --force --all
```

> **警告**：`filter-repo` 会改写整个仓库历史，所有协作者需要重新克隆。**轮换密钥永远优先于清洗历史**——因为任何一次克隆、任何一次 fork、任何一个 CI 缓存都可能已经保存了它。

预防措施：`.env` 加入 `.gitignore`、仓库开启 Push protection、使用 Secrets Manager 而非配置文件、提交前用 `git diff --staged` 复查。

### 9.4 分支保护与规则集

在 **Settings → Branches**（或新版 **Rules / Rulesets**）中可为 `main` 配置：

- 要求 Pull Request 才能合并（禁止直接 push）。
- 要求至少 N 名评审人批准。
- 要求 CODEOWNERS 审批。
- 要求状态检查通过（CI 必须绿）。
- 要求提交签名。
- 禁止强制推送、禁止删除分支。
- 要求线性历史（配合 squash/rebase 合并）。
- 合并前必须解决全部对话。

**强烈建议**：任何多人协作仓库都应对 `main` 启用「需 PR + 需 CI 通过」两项保护。

### 9.5 代码扫描

- **CodeQL**：GitHub 官方语义化静态分析，可配置 `codeql-action` 在 CI 中运行，覆盖常见漏洞类别（注入、路径穿越、硬编码密钥等）。
- **第三方生态**：SonarCloud、Snyk、Trivy 等均提供 Action，可嵌入同一工作流。

### 9.6 提交签名

```bash
# GPG 方式
gpg --full-generate-key
gpg --list-secret-keys --keyid-format=long
git config --global user.signingkey <KEY_ID>
git config --global commit.gpgsign true
git config --global tag.gpgsign true

# 或使用 SSH 签名（Git 2.34+，更简单）
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

之后把公钥添加到 GitHub 的 **SSH and GPG keys**，提交会显示 **Verified** 徽章。配合分支保护的「要求签名」可防止身份伪造。

### 9.7 企业级治理要点

- **权限最小化**：按团队与仓库授予 Write / Triage / Maintain，避免全员 Admin。
- **审计日志**：导出并留存关键操作记录。
- **合规策略**：统一许可证白名单、统一密钥管理规范。
- **SBOM**：通过依赖图谱或第三方工具生成软件物料清单。
- **灾难恢复**：定期镜像备份关键仓库（`git clone --mirror` + 定时拉取）。

---

## 10. 进阶命令与高级技巧

### 10.1 stash：临时收纳改动

```bash
git stash push -m "登录页改到一半"
git stash list
git stash show -p stash@{0}
git stash pop                 # 恢复并删除
git stash apply stash@{0}     # 恢复但保留记录
git stash drop stash@{0}
git stash clear
git stash push -u             # 含未跟踪文件
```

### 10.2 cherry-pick：精确搬运提交

```bash
git cherry-pick abc1234            # 把某提交应用到当前分支
git cherry-pick abc1234 def5678    # 多个提交
git cherry-pick abc1234..def5678   # 区间（不含 abc1234）
git cherry-pick -x abc1234         # 在信息中记录来源，便于追溯
```

典型用途：紧急 hotfix 需要同时出现在 `main` 与 `release/2.x`。

### 10.3 reflog：误操作的后悔药

```bash
git reflog                          # 查看 HEAD 的所有历史位置
git reset --hard HEAD@{3}           # 回到某个历史位置
git branch recover-branch abc1234   # 从某个提交恢复分支
```

reflog 默认保留 90 天，是找回「误删分支 / 误 reset」的最强手段。

### 10.4 交互式 rebase 整理历史

```bash
git rebase -i HEAD~4
```

```
pick a111111 feat: 新增登录表单
fixup a222222 fix: 修正拼写错误
reword a333333 feat: 接入验证码接口
drop a444444 wip: 临时调试代码
```

### 10.5 worktree：同一仓库多工作区

```bash
git worktree add ../repo-hotfix hotfix/urgent
git worktree list
git worktree remove ../repo-hotfix
```

适合「正在写大功能，但需要紧急修 bug」的场景，无需 `stash` 或重复克隆。

### 10.6 子模块与 Monorepo

```bash
git submodule add git@github.com:org/lib.git libs/lib
git submodule update --init --recursive
git clone --recurse-submodules git@github.com:org/main.git
git submodule update --remote libs/lib    # 更新到子模块最新提交
```

注意子模块**记录的是固定提交号**，更新需要显式提交一次。

大型项目也可考虑 Monorepo + 工作区工具（npm workspaces、pnpm、Bazel、Turborepo、Nx），在「代码统一治理」与「构建效率」之间取舍。

### 10.7 大文件与性能优化

```bash
# Git LFS：管理二进制大文件
git lfs install
git lfs track "*.psd" "*.zip" "*.mp4"
git add .gitattributes
git lfs ls-files

# 部分克隆 / 稀疏检出（大仓库提速）
git clone --filter=blob:none git@github.com:org/huge.git
git sparse-checkout set src/api

# 仓库瘦身
git gc --aggressive --prune=now
git count-objects -vH
```

### 10.8 bisect：二分定位引入问题的提交

```bash
git bisect start
git bisect bad                # 当前版本有问题
git bisect good v1.0.0        # 该版本正常
# Git 自动检出中间提交，你只需运行测试并标记 good / bad
git bisect good               # 或 git bisect bad
git bisect reset              # 结束并回到原分支

# 自动化二分
git bisect run npm test       # 用脚本自动判定
```

在数千个提交中定位回归，`bisect` 通常只需十余次测试。

### 10.9 其他实用命令

```bash
git diff --check                    # 检查行尾空白与冲突标记残留
git log --follow -- file            # 跨重命名追踪文件
git restore --source=HEAD~2 -- file # 从历史版本恢复单个文件
git range-diff main...feature       # 比较两个分支差异集
git show --stat abc1234             # 单次提交的改动统计
git ls-files                       # 列出被跟踪文件
git grep "TODO"                    # 在全部版本中搜索
```

---

## 11. 疑难排错手册

### 11.1 detached HEAD（游离头指针）

**症状**：`git status` 提示 `You are in 'detached HEAD' state`。

**原因**：直接 `checkout` 了某个提交或标签，而非分支。

**处置**：

```bash
# 希望保留改动
git switch -c temp-branch

# 不需要改动，回到分支
git switch main
```

### 11.2 push 被拒：non-fast-forward

**症状**：`! [rejected] main -> main (non-fast-forward)`。

**原因**：远程有你本地没有的提交。

**处置**：

```bash
git fetch origin
git rebase origin/main          # 或 git merge origin/main
# 解决冲突后
git push                        # 不要直接 --force
```

### 11.3 换行符混乱（CRLF / LF）

**症状**：文件未改动却显示全部行变更；团队成员互相冲突。

**处置**：在仓库根目录添加 `.gitattributes`：

```gitattributes
* text=auto eol=lf
*.sh   text eol=lf
*.bat  text eol=crlf
*.png  binary
*.jpg  binary
*.pdf  binary
```

然后统一规范化一次：

```bash
git add --renormalize .
git commit -m "chore: 统一换行符为 LF"
```

### 11.4 中文文件名显示为转义码

**症状**：`\346\226\207\344\273\266.md`。

**处置**：

```bash
git config --global core.quotepath false
```

### 11.5 忽略规则不生效

**原因**：文件已被 Git 跟踪。

**处置**：

```bash
git rm --cached <file>        # 停止跟踪，保留本地文件
git commit -m "chore: 停止跟踪本地配置文件"
```

### 11.6 pull 引发冲突

```bash
git pull --rebase
# 解决冲突
git add <files>
git rebase --continue
# 若越解越乱，可随时中止
git rebase --abort
```

### 11.7 网络与代理问题

```bash
# HTTP(S) 走代理
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
git config --global --unset http.proxy      # 取消

# SSH 走 443 端口（22 被封时）
ssh-keyscan -p 443 ssh.github.com >> ~/.ssh/known_hosts
# 在 ~/.ssh/config 中添加：
# Host ssh.github.com
#   HostName ssh.github.com
#   Port 443
#   User git
```

### 11.8 仓库体积异常增长

```bash
git count-objects -vH               # 查看对象体积
git lfs ls-files                    # 检查 LFS 使用情况
git gc --prune=now                  # 垃圾回收
```

长期解决：用 Git LFS 管理二进制、`.gitignore` 排除构建产物、必要时用 `git filter-repo` 清理历史（见 9.3 节警告）。

### 11.9 常见错误速查

| 现象 | 常见原因 | 解决 |
| --- | --- | --- |
| `Permission denied (publickey)` | 公钥未添加 / 用错密钥 | 检查 `ssh -T git@github.com` 与 `~/.ssh/config` |
| `Please tell me who you are` | 未配置身份 | `git config user.name/email` |
| `src refspec main does not match any` | 本地分支名不同或无提交 | `git branch` 确认后重推 |
| `error: failed to push` | 远程领先 | `git pull --rebase` 再推 |
| `CONFLICT (content): Merge conflict` | 语义冲突 | 手动解决后 `git add` + `--continue` |
| `fatal: not a git repository` | 目录非仓库 | `git init` 或进入正确目录 |
| `index.lock exists` | 上次命令异常中断 | 确认无 Git 进程后删除 `.git/index.lock` |
| 提交作者显示陌生邮箱 | 仓库级配置覆盖 | `git config --list --show-origin` 排查 |

---

## 12. 工程化最佳实践

### 12.1 仓库初始化检查单

- [ ] `README.md`：一句话定位 + 5 分钟上手指南
- [ ] `LICENSE`：明确许可
- [ ] `.gitignore`：按语言与工具配置
- [ ] `.gitattributes`：固定换行符与二进制标记
- [ ] `CONTRIBUTING.md`：贡献流程与规范
- [ ] `SECURITY.md`：漏洞上报方式
- [ ] CI 工作流：至少包含 lint + test
- [ ] 分支保护：`main` 需 PR + 需 CI 通过
- [ ] Secret scanning 与 Push protection 已开启

### 12.2 提交与分支规范

| 项目 | 约定 |
| --- | --- |
| 分支命名 | `feat/xxx`、`fix/issue-128`、`chore/xxx`、`release/v1.2` |
| 提交信息 | Conventional Commits，首行 ≤ 72 字符 |
| 提交粒度 | 一次提交一个逻辑单元，可独立回滚 |
| 分支寿命 | 功能分支尽量 < 3 天 |
| 合并方式 | Squash merge 保持主干线性；release 用 merge 保留节点 |

### 12.3 Code Review 检查单

- [ ] 改动是否与 PR 描述一致、范围是否过大
- [ ] 是否有调试代码、注释掉的代码、硬编码配置
- [ ] 是否有敏感信息（密钥、内网地址、个人信息）
- [ ] 边界与异常路径是否处理
- [ ] 是否有配套测试，测试是否真的能失败
- [ ] 命名与接口设计是否清晰
- [ ] 文档与 CHANGELOG 是否需要同步
- [ ] 是否引入新的依赖，其许可证与安全状况如何
- [ ] 是否存在性能与并发隐患
- [ ] 是否向后兼容，是否需要迁移说明

### 12.4 上线前检查单

- [ ] CI 全绿，含单元、集成、端到端测试
- [ ] 无未解决的评审对话
- [ ] 版本号已按 SemVer 升级
- [ ] `CHANGELOG.md` 已更新
- [ ] 数据库迁移脚本可回滚
- [ ] 监控与告警已就位，具备回滚方案
- [ ] 敏感配置通过 Secrets 注入，未打包进产物

### 12.5 学习路径建议

| 阶段 | 目标 | 重点 |
| --- | --- | --- |
| 入门（1–2 周） | 能独立完成 add / commit / push | 三区模型、提交规范、.gitignore |
| 进阶（1–2 月） | 能参与团队协作 | 分支、PR、Code Review、冲突解决 |
| 熟练（3–6 月） | 能搭建工程化流程 | Actions、分支保护、自动化发布 |
| 精通（6 月+） | 能治理大型仓库与事故 | rebase 历史手术、reflog、filter-repo、安全治理 |

---

## 附录 A. 命令速查表

| 场景 | 命令 |
| --- | --- |
| 初始化仓库 | `git init` |
| 克隆仓库 | `git clone <url>` |
| 查看状态 | `git status -sb` |
| 暂存文件 | `git add <file>` / `git add -p` |
| 提交 | `git commit -m "msg"` |
| 修改上次提交 | `git commit --amend --no-edit` |
| 查看差异 | `git diff` / `git diff --staged` |
| 查看历史 | `git log --oneline --graph --decorate --all` |
| 创建并切换分支 | `git switch -c <branch>` |
| 切换分支 | `git switch <branch>` |
| 合并分支 | `git merge <branch>` |
| 变基 | `git rebase <branch>` |
| 交互式整理提交 | `git rebase -i HEAD~N` |
| 拉取远程 | `git fetch origin` |
| 拉取并集成 | `git pull --rebase` |
| 推送 | `git push -u origin <branch>` |
| 安全强制推送 | `git push --force-with-lease` |
| 删除远程分支 | `git push origin --delete <branch>` |
| 打标签 | `git tag -a v1.0.0 -m "msg"` |
| 撤销工作区改动 | `git restore <file>` |
| 取消暂存 | `git restore --staged <file>` |
| 抵消历史提交 | `git revert <sha>` |
| 丢弃本地提交 | `git reset --hard HEAD~N` |
| 暂存改动 | `git stash push -m "msg"` |
| 恢复暂存 | `git stash pop` |
| 搬运提交 | `git cherry-pick <sha>` |
| 找回误删 | `git reflog` |
| 二分定位 | `git bisect start` |
| 查看责任人 | `git blame <file>` |
| 追踪单文件历史 | `git log --follow -- <file>` |
| 检查忽略规则 | `git check-ignore -v <file>` |
| 清理远程失效分支 | `git fetch --prune` |
| 垃圾回收 | `git gc --prune=now` |
| 查看仓库体积 | `git count-objects -vH` |
| 配置别名 | `git config --global alias.lg "log --oneline --graph"` |

---

## 附录 B. 术语表

| 术语 | 释义 |
| --- | --- |
| Repository（仓库） | 包含全部版本历史与工作区的项目目录 |
| Working Directory | 当前可见、可编辑的文件区域 |
| Staging Area / Index | 提交前的暂存区，决定下次提交的内容 |
| Commit | 一次不可变的快照，附带作者、时间与信息 |
| Tree / Blob | Git 对象：目录结构 / 文件内容 |
| SHA | 提交与对象的内容哈希标识 |
| Branch | 指向某个提交的可移动引用 |
| HEAD | 指向当前分支（或提交）的指针 |
| Remote | 远程仓库的本地别名，如 `origin` |
| Upstream | 本地分支跟踪的远程分支 |
| Fetch | 下载远程对象与引用，不改动工作区 |
| Pull | Fetch + Merge/Rebase |
| Push | 上传本地提交到远程 |
| Merge | 将两个分支历史汇合 |
| Rebase | 将提交搬到另一基点，重写历史 |
| Fast-forward | 目标分支无新提交时的指针直移 |
| Conflict | 双方改动无法自动合并的状态 |
| Tag | 固定指向某个提交的标记，常用于版本发布 |
| Fork | 在自己名下复制他人仓库 |
| Pull Request | 请求将自己分支的改动合入目标分支 |
| Issue | 任务、缺陷或讨论的记录单元 |
| CI / CD | 持续集成 / 持续交付 |
| Runner | 执行工作流的机器 |
| Workflow | GitHub Actions 的自动化流程定义 |
| SBOM | 软件物料清单，记录全部依赖组件 |
| Conventional Commits | 约定式提交信息规范 |
| SemVer | 语义化版本号规范 |

---

## 附录 C. 延伸学习资源

1. **Pro Git（官方书籍，含中文版）** — [git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)
   系统性理解 Git 内部原理的最佳材料，第 1、3、7 章尤其值得精读。
2. **Git 官方参考手册** — [git-scm.com/docs](https://git-scm.com/docs)
   命令行为的最终依据，遇到歧义时查阅此处。
3. **GitHub 官方文档** — [docs.github.com](https://docs.github.com)
   平台功能、安全策略与 Actions 语法的权威来源。
4. **GitHub Skills** — [skills.github.com](https://skills.github.com)
   交互式实操课程，覆盖 PR、Actions、Pages 等主题。
5. **Learn Git Branching** — [learngitbranching.js.org](https://learngitbranching.js.org)
   可视化分支练习，把 merge / rebase / cherry-pick 玩成肌肉记忆。
6. **Conventional Commits 规范** — [conventionalcommits.org](https://www.conventionalcommits.org)
7. **Keep a Changelog** — [keepachangelog.com](https://keepachangelog.com)
8. **Semantic Versioning** — [semver.org](https://semver.org)

---

> **结语**：Git 的学习曲线陡峭在「心智模型」，而不是命令数量。把三区模型、提交对象与分支指针这三件事想透，剩下的命令只是在不同场景下移动这些指针。工具会更新，界面会改版，但**「小步提交、清晰历史、可回滚、可追溯」**这十六字原则，会在你的整个职业生涯里持续生效。
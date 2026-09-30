# 

> **版本**：v1.0　|　**适用对象**：零基础到中高级工程师、DevOps 与平台运维人员　|　**阅读方式**：线性阅读 + 按需查阅附录
>
> **阅读须知**：Git 命令与 GitLab CI 语法相对稳定，本文命令可长期复用；GitLab 按月发布，**功能所属订阅层级（Free / Premium / Ultimate）与网页界面会随版本调整**，涉及套餐边界与界面操作处，请以 [GitLab 官方文档](https://docs.gitlab.com) 最新说明为准。文中所有示例均可在测试环境安全复现。

---

## 目录

- [1. 概念基础](#1-概念基础)
- [2. 环境准备与账号配置](#2-环境准备与账号配置)
- [3. 本地 Git 核心操作](#3-本地-git-核心操作)
- [4. 远程协作与合并请求](#4-远程协作与合并请求)
- [5. 分支模型：GitLab Flow](#5-分支模型gitlab-flow)
- [6. 项目与团队协作](#6-项目与团队协作)
- [7. CI/CD 核心](#7-cicd-核心)
- [8. 部署与发布](#8-部署与发布)
- [9. 安全与合规](#9-安全与合规)
- [10. 私有化运维与进阶](#10-私有化运维与进阶)
- [11. 疑难排错手册](#11-疑难排错手册)
- [12. 工程化最佳实践](#12-工程化最佳实践)
- [附录 A. 命令速查表](#附录-a-命令速查表)
- [附录 B. .gitlab-ci.yml 关键字速查](#附录-b-gitlab-ciyml-关键字速查)
- [附录 C. 术语表](#附录-c-术语表)
- [附录 D. 延伸学习资源](#附录-d-延伸学习资源)

---

## 1. 概念基础

### 1.1 GitLab 是什么

GitLab 是一个**一体化 DevSecOps 平台**，把「代码托管 + 代码评审 + CI/CD + 制品管理 + 安全扫描 + 项目管理 + 监控反馈」收敛到单一应用、单一数据模型、单一权限体系中。

这一点决定了它的使用方式：你在别的平台可能需要拼装 5–8 个工具，而在 GitLab 里，一个 Issue 可以直接转化为分支、合并请求、流水线、部署记录与发布说明，全程同源追溯。

### 1.2 三种部署形态

| 形态 | 说明 | 适用 |
| --- | --- | --- |
| **GitLab.com** | 官方多租户 SaaS，免运维 | 中小团队、快速起步 |
| **Self-Managed（自建）** | 部署在自己的服务器或私有云 | 有合规、内网、数据主权要求的组织 |
| **Dedicated** | 官方托管的单租户实例 | 要求隔离且不想自建运维的中大型团队 |

自建实例又分两个发行版：**GitLab CE（社区版，MIT/开源）** 与 **GitLab EE（企业版，含付费功能）**。本文不预设你购买任何付费层级，凡涉及层级差异处都会标注。

### 1.3 GitLab 与 GitHub 的关键差异

| 维度 | GitLab | GitHub |
| --- | --- | --- |
| 定位 | 一体化 DevSecOps 平台 | 代码托管 + 社区生态 |
| 合并请求名称 | Merge Request（MR） | Pull Request（PR） |
| CI/CD | 原生内建，`.gitlab-ci.yml` | 需借助 GitHub Actions |
| 仓库组织 | Group / Subgroup 多级分组 | Organization / Team |
| 权限粒度 | 角色 + 受保护分支 + 受保护环境 | Role + Branch protection / Rulesets |
| 自建方案 | 成熟的自建发行版 | 仅 GitHub Enterprise Server |
| 内建制品 | Container/Package Registry、Pages 均原生 | 多依赖第三方 |

> 结论：**需要内网自建、追求「一个平台全包」选 GitLab；看重开源社区网络与生态集成选 GitHub。** 两者底层都是 Git，技能可迁移。

### 1.4 自建实例的架构组件

理解这些组件，运维问题就解决了一半：

| 组件 | 职责 |
| --- | --- |
| NGINX | 反向代理、TLS 终止、静态资源 |
| Puma（Rails） | Web 应用主进程，处理 HTTP/API |
| Sidekiq | 后台任务队列（邮件、Webhook、CI 调度等） |
| Gitaly | Git 仓库读写服务，所有 Git 操作的唯一入口 |
| PostgreSQL | 主数据库（项目、用户、CI 元数据） |
| Redis | 缓存与 Sidekiq 队列 |
| GitLab Shell | 处理 `git push/pull` 的 SSH 通道 |
| Container Registry | 镜像仓库服务 |
| GitLab Runner | 执行 CI/CD 作业的独立程序（**不随主程序分发，需单独安装**） |
| Workhorse | 处理大文件上传、Git HTTP 代理等高负载请求 |

**关键认知**：Runner 是独立进程，可以装在任意机器上，甚至多台机器组成集群。这是理解 CI/CD 的第一块拼图。

### 1.5 订阅层级速览

| 层级 | 典型能力 |
| --- | --- |
| **Free** | 无限私有仓库、基础 MR、基础 CI/CD、Container Registry、Pages |
| **Premium** | 合并列车、审计事件、推规则、高级合并选项、优先支持、多级流水线治理 |
| **Ultimate** | 安全扫描全家桶（SAST/DAST/依赖/密钥/许可证）、合规框架、组合看板、价值流分析 |

> **务必注意**：GitLab 会定期调整功能归属层级，上表仅为心智地图。采购或架构设计前，请查阅官方「GitLab Pricing / Features by tier」页面确认。

### 1.6 对象模型

```
实例（Instance）
 └── 组（Group）── 可嵌套子组（Subgroup）
      └── 项目（Project）
           ├── 代码仓库（Repository）
           ├── 合并请求（Merge Request）
           ├── 议题（Issue）/ 任务（Task）
           ├── 流水线（Pipeline）→ 作业（Job）
           ├── 环境（Environment）/ 部署（Deployment）
           ├── 制品（Package / Container Registry）
           └── Wiki / Snippet / Pages
```

**Group 是 GitLab 的组织核心**：成员权限、变量、Runner、里程碑、Epic 都可在组级统一管理，子项目继承。规划好 Group 层级，等于规划好了治理结构。

---

## 2. 环境准备与账号配置

### 2.1 使用 GitLab.com

1. 注册账号并验证邮箱。
2. 创建 **Group**（团队）→ 在组下创建 **Project**。
3. 免费层级有存储与 CI 分钟数配额，超额会暂停流水线，具体额度以官方定价页为准。

### 2.2 私有化部署（Linux 包 / Omnibus）

适用于 Ubuntu / Debian / RHEL 系单机部署，是官方推荐路径：

```bash
# 1. 安装依赖
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates tzdata perl

# 2. 配置官方仓库并安装（CE 社区版）
curl -fsSL https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ce

# 3. 初始化配置（首次 reconfigure 会自动生成证书与初始 root 密码）
sudo gitlab-ctl reconfigure

# 4. 查看服务状态
sudo gitlab-ctl status

# 5. 获取初始 root 密码（新版本在首次安装时打印；老版本可查该文件）
sudo cat /etc/gitlab/initial_root_password
```

安装后立即做的三件事：

1. 用 `root` 登录并**修改密码**；
2. 删除 `/etc/gitlab/initial_root_password`；
3. 配置真实域名与 TLS 证书（编辑 `/etc/gitlab/gitlab.rb` 中的 `external_url`，然后 `gitlab-ctl reconfigure`）。

### 2.3 Docker 单机部署

```bash
docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 2222:22 \
  --name gitlab \
  --restart always \
  --shm-size 256m \
  --volume /srv/gitlab/config:/etc/gitlab \
  --volume /srv/gitlab/logs:/var/log/gitlab \
  --volume /srv/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

数据全部位于三个挂载卷中，**务必备份 `/srv/gitlab/config/gitlab-secrets.json`**，丢失它会导致 2FA、Runner、CI 变量等加密数据无法恢复。

### 2.4 本地 Git 与身份配置

```bash
git config --global user.name  "你的名字"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input        # Windows 用 true
git config --global pull.rebase false
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global core.quotepath false       # 中文文件名正常显示
```

生成 SSH 密钥并添加到 GitLab（**头像 → Preferences → SSH Keys**）：

```bash
ssh-keygen -t ed25519 -C "you@example.com"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub        # 复制输出内容到 GitLab

# 验证
ssh -T git@gitlab.com            # 自建实例用 git@gitlab.example.com
```

> **认证事实**：GitLab 同样**不支持账号密码推送**，必须使用 SSH 密钥或访问令牌（Personal / Project / Group Access Token）。自建实例可选 LDAP / SAML / OAuth 单点登录。

### 2.5 访问令牌体系

| 令牌类型 | 用途 | 建议 |
| --- | --- | --- |
| **Personal Access Token（PAT）** | 调用 API、CLI 登录、HTTPS 推送 | 限定 scope 与有效期 |
| **Project Access Token** | 项目级机器人账号（如自动化脚本） | 比共享 PAT 更安全 |
| **Group Access Token** | 组级自动化 | 同上 |
| **Deploy Token** | CI/CD 只读拉取制品 | 只授予读权限 |
| **Deploy Key** | 部署机只读访问仓库 | 机器身份首选 |
| **Runner Token** | Runner 注册与通信 | 由界面生成，禁止外传 |
| **CI_JOB_TOKEN** | 流水线内跨项目拉取 | 自动注入，只读、短时有效 |

安全守则：最小权限、短期有效、绝不入库、泄露即轮换。CI 中一律使用 **CI/CD Variables**（Settings → CI/CD → Variables）而非硬编码。

---

## 3. 本地 Git 核心操作

> 本章是 Git 速成，与平台无关；已熟悉 Git 的读者可跳至第 4 章。

### 3.1 三区模型

```
工作区  --git add-->  暂存区  --git commit-->  本地仓库  --git push-->  远程仓库
```

`git commit` 提交的是**暂存区内容**，不是工作区当前状态。这是新手最常见的认知偏差。

### 3.2 标准工作流

```bash
git init                       # 或 git clone <url>
git switch -c feature/login    # 建功能分支
# ...修改代码...
git status
git add -p                     # 逐块暂存，让提交可读
git commit -m "feat(auth): 新增验证码登录"
git push -u origin feature/login
```

### 3.3 查看与追溯

```bash
git diff                 # 工作区 vs 暂存区
git diff --staged        # 暂存区 vs 上次提交
git log --oneline --graph --decorate --all
git show <sha>
git blame <file>
git log --follow -- <file>
```

### 3.4 撤销场景对照

| 场景 | 命令 |
| --- | --- |
| 丢弃工作区改动 | `git restore <file>` |
| 取消暂存 | `git restore --staged <file>` |
| 改写上次提交信息（未推送） | `git commit --amend -m "新信息"` |
| 抵消已推送的提交 | `git revert <sha>`（生成反向提交） |
| 丢弃本地未推送提交 | `git reset --hard HEAD~N`（危险） |
| 临时收纳改动 | `git stash push -m "说明"` |
| 找回误删分支 | `git reflog` |

### 3.5 提交信息规范（Conventional Commits）

```
<type>(<scope>): <简短描述>

<正文：解释动机>

<页脚：Closes #12 / BREAKING CHANGE: ...>
```

常用 `type`：`feat`、`fix`、`docs`、`style`、`refactor`、`perf`、`test`、`build`、`ci`、`chore`。

铁律三条：首行 ≤ 72 字符、祈使语气、一次提交只做一件事。正文写 **why**，不复述 diff 里的 **what**。

### 3.6 .gitignore

只对未跟踪文件生效；已被跟踪的文件需 `git rm --cached <file>` 后才会停止跟踪。

```gitignore
node_modules/
__pycache__/
.venv/
dist/
build/
*.log
.env
!.env.example
*.pem
*.key
.DS_Store
.idea/
```

调试工具：`git check-ignore -v <file>` 可显示命中规则。

---

## 4. 远程协作与合并请求

### 4.1 远程管理

```bash
git remote -v
git remote add origin git@gitlab.com:group/project.git
git remote add upstream git@gitlab.com:original/project.git   # Fork 工作流
git fetch --prune
```

### 4.2 push / fetch / pull

| 命令 | 作用 | 安全性 |
| --- | --- | --- |
| `git fetch` | 仅下载远程引用与对象 | 最安全 |
| `git pull` | fetch + 合并（merge/rebase） | 可能产生合并提交或冲突 |
| `git push` | 上传本地提交 | 被拒时先同步 |

推荐节奏：开工 `git pull --rebase`，收工 `git push`，不确定时先 `git fetch` 再看 `git log main..origin/main`。

### 4.3 Fork 工作流（参与外部项目）

```bash
# 1. 在 GitLab 网页 Fork 后克隆自己的副本
git clone git@gitlab.com:your-name/project.git
cd project
git remote add upstream git@gitlab.com:original/project.git

# 2. 同步上游
git fetch upstream
git switch main
git rebase upstream/main
git push origin main

# 3. 开发
git switch -c fix/issue-42
git add -p && git commit -m "fix(parser): 修正空值解析崩溃"
git push -u origin fix/issue-42

# 4. 在网页上向 upstream 发起 Merge Request
```

GitLab 的 MR 支持「允许目标项目的维护者向你的源分支推送」选项，方便评审人直接帮你补丁。

### 4.4 Merge Request 完整流程

1. **建分支**：从最新 `main` 切出 `feat/xxx` 或 `fix/issue-42`。
2. **开发自检**：跑测试、跑 lint、确认无调试代码与敏感信息。
3. **推送并创建 MR**：`git push -u origin feat/xxx`，网页端 **Merge requests → New merge request**。
4. **填写模板**：动机、改动点、影响范围、验证方式、关联 Issue（`Closes #42`）。
5. **评审迭代**：回应每条讨论，必要时 `git commit --fixup` 追加提交。
6. **CI 通过 + 审批满足 + 冲突已解** → 合并。
7. **收尾**：勾选「删除源分支」，同步主干。

**MR 标题建议沿用约定式提交**，合并后主干历史自动整洁。

### 4.5 MR 的评审能力

| 功能 | 说明 |
| --- | --- |
| 行内评论 / 讨论串 | 精确到 diff 的某一行提问 |
| **建议（Suggestion）** | 评审人给出可一键应用的代码补丁 |
| 解决讨论（Resolve） | 逐条销项，可配置「必须全部解决才能合并」 |
| CODEOWNERS | 自动指派路径负责人并要求其批准 |
| 变更对比 | 按提交、按整体、按文件查看 |
| 草稿（Draft:） | 标题前缀 `Draft:` 即进入草稿态，禁止合并 |
| 合并前检查清单 | 流水线状态、冲突、审批、讨论项一目了然 |

### 4.6 快速命令（Quick Actions）

在 Issue / MR 的评论框中直接输入，回车即生效：

| 命令 | 作用 |
| --- | --- |
| `/assign @user` | 指派负责人 |
| `/label ~bug ~backend` | 添加标签 |
| `/milestone %v1.2` | 设定里程碑 |
| `/due 2026-01-31` | 设定截止日期 |
| `/estimate 4h` `/time_spent 1h` | 工时估算与登记 |
| `/weight 3` | 设定权重 |
| `/draft` | 切换草稿状态 |
| `/close` `/reopen` | 关闭 / 重开 |
| `/merge` | 合并（可带参数如 `/merge squash`） |
| `/copy_metadata from #33` | 复制标签与里程碑 |
| `/subscribe` | 订阅通知 |
| `/target_branch develop` | 切换目标分支 |

### 4.7 受保护分支（Protected Branches）

路径：**Settings → Repository → Protected branches**。

核心配置：

- 谁可以**合并**：No one / Developers + Maintainers / Maintainers / 指定用户或组。
- 谁可以**推送**：同上。
- 是否允许**强制推送**。
- 是否要求 **CODEOWNERS 批准**。
- 是否要求**签名提交**。

**默认实践**：`main` 与 `production` 设为受保护，仅允许通过 MR 合入，禁止强制推送。

---

## 5. 分支模型：GitLab Flow

### 5.1 三条原则

1. **上游优先（Upstream first）**：改动先进主干，再流向下游分支或环境。
2. **分支与环境一一对应**：`pre-production` / `production` 分支映射到同名环境。
3. **MR 是唯一入口**：任何代码进入受保护分支都经过评审与流水线。

### 5.2 环境分支模型（持续部署团队）

```
feature/* ──MR──▶ main ──MR──▶ pre-production ──MR──▶ production
                     │
                     └──▶ 部署到 staging 环境
```

- `main` 始终可部署，流水线自动部署到 staging。
- 需要预发验证时，向 `pre-production` 发起 MR。
- 正式上线时，向 `production` 发起 MR（可配置需人工批准）。

### 5.3 发布分支模型（有版本节奏的团队）

```
feature/* ──▶ main ──▶ release/2.1 ──打标签 v2.1.0──▶ 交付
                 │
                 └──hotfix/*──▶ release/2.1（并 cherry-pick 回 main）
```

发布分支一旦切出，只接受修复；新功能继续在 `main` 积累到下个版本。

### 5.4 合并方法

项目设置：**Settings → Merge requests → Merge method**。

| 方法 | 历史形态 | 说明 |
| --- | --- | --- |
| **Merge commit** | 有合并节点 | 保留完整协作轨迹，历史最真实 |
| **Fast-forward merge** | 线性 | 要求源分支已 rebase，无合并提交 |
| **Squash commits** | 线性 + 单节点 | 把分支内提交压成一个，可与上述任一方式叠加 |

搭配开关：「删除源分支」建议默认开启；「必须线性历史」可与 `git rebase` 工作流配合；**合并列车（Merge Train）** 可让多个 MR 依次排队验证后合入（属付费层级功能）。

**选择建议**：内部业务项目用 **Squash + Merge commit**，保留单功能单节点且不丢上下文；追求纯线性历史的库项目用 **Fast-forward**。

### 5.5 冲突解决

```
<<<<<<< HEAD
当前分支内容
=======
被合并分支内容
>>>>>>> feature/other
```

处置：手动编辑取舍 → `git add <file>` → `git merge --continue` 或 `git rebase --continue`；想放弃则 `git merge --abort` / `git rebase --abort`。

GitLab MR 页面也提供**在线冲突解决**工具，适合小冲突；复杂冲突请在本地用 IDE 处理。

预防：小步提交、分支寿命 < 3 天、统一格式化工具（Prettier / Black / gofmt）。

### 5.6 变基与历史整理

```bash
git rebase main                  # 同步主干
git rebase -i HEAD~3             # 整理最近 3 个提交
git push --force-with-lease      # 安全的强制推送
```

**黄金法则**：绝不 rebase 已被他人依赖的共享提交。`--force-with-lease` 优于 `--force`。

### 5.7 标签与版本号

采用 **SemVer**：`MAJOR.MINOR.PATCH`。

```bash
git tag -a v2.1.0 -m "v2.1.0：新增导出功能"
git push origin v2.1.0
```

在 GitLab 中可为标签创建 **Release**（见 8.5 节），自动生成变更列表并挂载产物。

---

## 6. 项目与团队协作

### 6.1 Issue

高质量 Issue 结构：**标题（what）+ 环境信息 + 复现步骤 + 期望行为 + 实际行为 + 日志/截图 + 期望的解决方向**。

可用 `/label`、`/assign`、`/milestone`、`/due`、`/weight` 快速填充元数据。Issue 支持勾选任务清单，可转换为合并请求（**Create merge request** 按钮会自建分支与 MR，标题自动关联 `Closes #`）。

### 6.2 标签与议题板（Issue Board）

推荐标签体系：

| 类别 | 示例 |
| --- | --- |
| 类型 | `bug`、`feature`、`docs`、`question`、`chore` |
| 优先级 | `P0`、`P1`、`P2` |
| 状态 | `todo`、`doing`、`blocked`、`verify` |
| 模块 | `ui`、`api`、`infra`、`perf` |
| 新人友好 | `good first issue`、`help wanted` |

议题板是「标签驱动的看板」：每一列对应一个标签，拖动卡片等于改标签，非常适合把 `todo → doing → verify → done` 固化为团队工作流。

### 6.3 里程碑与度量

里程碑（Milestone）聚合 Issue 与 MR，支持**燃尽图**与完成度统计。配合权重（Weight）与工时（Time tracking），可以粗粒度评估迭代容量。

### 6.4 层级化需求管理

```
Epic（跨项目主题，如「支付重构」）
 └── Epic（子主题）
      └── Issue（可交付单元）
           └── Task / Checklist（执行项）
```

组合看板、路线图、价值流分析等能力在付费层级提供，适合需要跨项目组合管理的组织。

### 6.5 Wiki / Snippets / Web IDE

- **Wiki**：独立 Git 仓库，适合架构设计、运维手册等长文档。
- **Snippets**：可版本化的代码片段，支持私有/内部/公开可见性。
- **Web IDE**：浏览器内的 VS Code 体验，适合小改动与评审后快速修补。

### 6.6 通知与待办

- **Notification**：全局/项目级订阅级别（Disabled / Participating / Watch / Global）。
- **To-Do List**：被指派、被 @、被请求评审时自动生成待办，处理完即归档。

### 6.7 项目模板

- **Issue 模板**：`.gitlab/issue_templates/Bug.md` 与 `Feature.md`。
- **MR 模板**：`.gitlab/merge_request_templates/Default.md`。
- 创建 Issue / MR 时从下拉框选择模板。

```markdown
<!-- .gitlab/issue_templates/Bug.md -->
## 环境
- 操作系统：
- 版本号：

## 复现步骤
1.
2.

## 期望行为
## 实际行为
## 日志 / 截图
```

---

## 7. CI/CD 核心

### 7.1 核心概念

| 概念 | 说明 |
| --- | --- |
| **Pipeline（流水线）** | 一次触发的完整流程，由多个阶段组成 |
| **Stage（阶段）** | 流水线的横向分组，同阶段作业并行 |
| **Job（作业）** | 最小执行单元，失败则流水线失败 |
| **Runner** | 执行作业的程序，需单独安装并注册 |
| **Artifact** | 作业产出的文件，可跨作业传递 |
| **Cache** | 加速用的中间文件缓存，不保证可靠 |
| **Environment** | 部署目标，形成部署历史与回滚入口 |

### 7.2 第一条流水线

在仓库根目录创建 `.gitlab-ci.yml`：

```yaml
stages:
  - test
  - build

hello:
  stage: test
  script:
    - echo "Hello GitLab CI"
    - uname -a

build:
  stage: build
  script:
    - echo "构建完成"
  artifacts:
    paths:
      - output/
```

推送到 GitLab 后，**CI/CD → Pipelines** 会自动出现流水线。YAML 语法错误时，可用内置的 **CI Lint**（CI/CD → Editor → Validate）校验。

### 7.3 Runner：安装与注册

Runner 是独立组件，安装在执行机器上：

```bash
# Debian/Ubuntu
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
sudo apt-get install gitlab-runner

# 验证
gitlab-runner --version
```

**注册（新版推荐流程）**：

1. 在 GitLab 界面创建 Runner：**Admin Area（或 Group/Project）→ Build → Runners → New runner**，得到形如 `glrt-xxxxxxxx` 的认证令牌。
2. 在执行机上运行：

```bash
sudo gitlab-runner register \
  --non-interactive \
  --url https://gitlab.example.com/ \
  --token glrt-xxxxxxxx \
  --executor docker \
  --docker-image alpine:latest \
  --description "docker-runner-01"
```

常用管理命令：

```bash
sudo gitlab-runner list          # 列出已注册 Runner
sudo gitlab-runner verify        # 校验连通性
sudo gitlab-runner unregister --name docker-runner-01
sudo gitlab-runner logs
```

**执行器选型**：

| 执行器 | 特点 | 适用 |
| --- | --- | --- |
| `shell` | 直接在宿主机执行 | 简单、性能好，但环境污染 |
| `docker` | 每次作业在容器中，环境干净 | **默认推荐** |
| `kubernetes` | 在 K8s 中动态起 Pod | 大规模、弹性伸缩 |
| `docker-autoscaler` | 弹性扩缩容机器 | 云端大规模 CI（替代已逐步淘汰的 docker-machine） |

**安全要点**：给涉及生产的 Runner 打上 **Protected** 标记（Runner 设置中开启），使其只执行受保护分支/标签的作业；配合 Job Tags（如 `tags: [docker, gpu]`）区分资源池。

### 7.4 .gitlab-ci.yml 语法全解

一个贴近真实项目的完整示例：

```yaml
default:
  image: node:20-alpine
  interruptible: true

workflow:
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_TAG

variables:
  npm_config_cache: "$CI_PROJECT_DIR/.npm"
  NODE_ENV: "test"

stages:
  - test
  - build
  - deploy

cache:
  key:
    files:
      - package-lock.json
  paths:
    - .npm/

test:
  stage: test
  parallel: 3
  script:
    - npm ci --cache .npm --prefer-offline
    - npm run lint
    - npm test
  coverage: '/All files\s*\|\s*([\d.]+)/'
  artifacts:
    when: always
    expire_in: 7 days
    paths:
      - coverage/
    reports:
      junit: reports/junit.xml
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

build:
  stage: build
  needs: [test]
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_TAG
  script:
    - npm run build
  artifacts:
    paths: [dist/]
    expire_in: 30 days

deploy:
  stage: deploy
  needs: [build]
  environment:
    name: production
    url: https://app.example.com
    deployment_tier: production
  script:
    - ./scripts/deploy.sh
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: manual
      allow_failure: false
```

要点解读：

- **`workflow:rules`** 控制「是否生成流水线」，避免同一提交重复触发 MR 流水线与分支流水线。
- **`needs`** 让作业不等整阶段完成即可跳跑，形成 DAG，显著缩短总时长。
- **`interruptible: true`** 允许新推送自动取消旧流水线，节省资源。
- **`when: manual` + `allow_failure: false`** 构成「上线闸门」。
- **`cache`** 用 lockfile 作为 key 文件，实现精确缓存失效。

### 7.5 变量体系

| 来源 | 例子 | 说明 |
| --- | --- | --- |
| 预定义变量 | `CI_COMMIT_SHA`、`CI_COMMIT_REF_NAME`、`CI_COMMIT_SHORT_SHA`、`CI_PIPELINE_SOURCE`、`CI_PROJECT_PATH`、`CI_REGISTRY_IMAGE`、`CI_JOB_TOKEN`、`CI_DEFAULT_BRANCH`、`CI_MERGE_REQUEST_IID`、`CI_PAGES_URL` | 系统自动注入 |
| 实例/组/项目变量 | `KUBE_TOKEN`、`API_KEY` | Settings → CI/CD → Variables |
| YAML 变量 | `NODE_ENV: test` | 定义在 `.gitlab-ci.yml` |
| `.env` 报告 | `dotenv` artifact | 作业间传递运行时变量 |

受保护变量的两个开关：

- **Protected**：只在受保护分支/标签的作业中可见；
- **Masked**：日志中自动打码（要求值为单行、满足字符集限制）。

**密钥黄金法则**：永远放 CI/CD Variables，永远不写进 YAML，永远不在 `script` 里 `echo`。

### 7.6 cache 与 artifacts 的区别

| 维度 | cache | artifacts |
| --- | --- | --- |
| 目的 | 加速（依赖包、构建中间物） | 传递结果（构建产物、测试报告） |
| 可靠性 | 可能丢失，不可依赖 | 保存在 GitLab，可靠 |
| 跨流水线 | 可复用 | 按过期时间管理 |
| 下载方式 | 自动按 key 恢复 | `needs` / `dependencies` 显式传递 |

经验法则：**依赖进 cache，结果进 artifacts**。把 `node_modules` 放 artifacts 是常见反模式，体积大且拖慢上传。

### 7.7 rules 语法

```yaml
job:
  script: ./test.sh
  rules:
    - if: $CI_COMMIT_TAG =~ /^v\d+\.\d+\.\d+$/      # 正则匹配标签
      when: always
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      changes:                                        # 相关文件变更才触发
        - src/**/*
        - package.json
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      exists:                                           # 文件存在才触发
        - Dockerfile
    - when: manual                                     # 兜底
      allow_failure: true
```

> `only/except` 仍可用但已停止演进，新项目请直接用 `rules`——表达力更强，且可逐条控制 `when` 与 `allow_failure`。

### 7.8 复用：include / extends / 组件

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml          # 官方模板
  - local: ci/build.yml                        # 仓库内文件
  - project: devops/ci-templates               # 其他项目的文件
    ref: v2
    file: /templates/docker.yml
  - remote: https://example.com/shared.yml

extends: .docker_build                          # 覆盖式继承

build_api:
  extends: .docker_build
  variables:
    IMAGE_NAME: api
```

`.gitlab-ci.yml` 中的 `!reference [.template, script]` 可局部引用片段；更结构化的做法是 **CI/CD Components**（`spec:inputs` 声明输入），适合在组织内沉淀标准化流水线。

### 7.9 子流水线与多项目流水线

```yaml
# 子流水线：在当前项目内动态生成并触发
monorepo-pipeline:
  stage: build
  trigger:
    include: generated/deploy.yml
    strategy: depend          # 子流水线失败影响父流水线

# 多项目流水线：触发另一个项目
deploy-infra:
  stage: deploy
  trigger:
    project: infra/terraform
    branch: main
    strategy: depend
```

### 7.10 并行与效率

```yaml
test:
  stage: test
  parallel:
    matrix:
      - NODE_VERSION: [18, 20, 22]
        DB: [postgres, mysql]      # 组合矩阵
  script:
    - npm test
```

- `parallel: N`：把单作业拆成 N 个并行分片（需测试框架支持分片）。
- `interruptible` + 合理 `cache` key + `needs` DAG，是提速三板斧。
- 大仓库可开启 Git 深度控制与「浅克隆」优化（Runner 的 `GIT_DEPTH`）。

### 7.11 定时、手动与延迟作业

```yaml
nightly:
  script: ./run-e2e.sh
  rules:
    - if: $CI_PIPELINE_SOURCE == "schedule"    # 由 CI/CD → Schedules 创建

rollback:
  script: ./rollback.sh
  when: delayed
  start_in: 30 minutes                        # 延迟执行，可随时取消
```

### 7.12 测试报告与质量门禁

- **JUnit 报告**：`artifacts.reports.junit` → MR 页面直接显示用例失败明细。
- **覆盖率**：`coverage:` 正则 + `coverage_report` → MR 显示覆盖率变化。
- **Code Quality**：接入 CodeClimate 兼容报告 → MR 展示质量退化项。
- **合并阻塞**：可在项目设置中要求「必须解决全部讨论」「必须通过指定检查」才能合并。

---

## 8. 部署与发布

### 8.1 环境（Environments）

```yaml
deploy_staging:
  stage: deploy
  environment:
    name: staging
    url: https://staging.example.com
    deployment_tier: staging
  script: ./deploy.sh
```

部署后在 **Operate → Environments** 可看到每个环境的部署历史、当前版本、关联 MR 与提交，支持**一键回滚**（Re-run 之前的部署作业）。

环境层级（deployment tier）：`production`、`staging`、`testing`、`development`、`other`，用于区分保护级别与仪表盘分组。

### 8.2 Review Apps（动态预览环境）

每个 MR 自动部署一份独立预览环境：

```yaml
review:
  stage: deploy
  script:
    - ./deploy_review.sh "$CI_COMMIT_REF_SLUG"
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.review.example.com
    on_stop: stop_review
    auto_stop_in: 1 week
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

stop_review:
  stage: deploy
  script: ./teardown_review.sh "$CI_COMMIT_REF_SLUG"
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    action: stop
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: manual
```

`on_stop` 作业负责销毁环境；`auto_stop_in` 让长期闲置的预览环境自动回收，控制成本。MR 页面会直接显示预览链接。

### 8.3 部署审批与冻结

- **受保护环境（Protected environments）**：指定允许部署的角色或用户，部署作业需人工批准（付费层级能力）。
- **部署冻结窗口（Deploy freeze）**：在规定时间段内禁止向生产环境部署，规避节假日事故。
- **审批流**：`when: manual` 是最朴素的审批闸门，任何层级都可用。

### 8.4 Release 与发布

```yaml
release:
  stage: deploy
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  needs: [build]
  rules:
    - if: $CI_COMMIT_TAG
  script:
    - echo "创建 Release"
  release:
    tag_name: $CI_COMMIT_TAG
    name: "Release $CI_COMMIT_TAG"
    description: "详见 CHANGELOG.md"
    assets:
      links:
        - name: "安装包"
          url: "${CI_PROJECT_URL}/-/jobs/artifacts/${CI_COMMIT_TAG}/download?job=build"
```

也可以用命令行：

```bash
glab release create v2.1.0 --notes "新增导出功能" dist/app.tar.gz
```

### 8.5 Container Registry

GitLab 内建镜像仓库，地址通常为 `registry.gitlab.com/<group>/<project>`（自建为 `gitlab.example.com:5050/...`）。

```yaml
build-image:
  stage: build
  image: docker:27
  services:
    - docker:27-dind
  variables:
    DOCKER_TLS_CERTDIR: "/certs"
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login -u "$CI_REGISTRY_USER" --password-stdin "$CI_REGISTRY"
    - docker build -t "$CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA" .
    - docker push "$CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA"
    - |
      if [ "$CI_COMMIT_BRANCH" = "$CI_DEFAULT_BRANCH" ]; then
        docker tag "$CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA" "$CI_REGISTRY_IMAGE:latest"
        docker push "$CI_REGISTRY_IMAGE:latest"
      fi
```

> 提示：内网环境无法访问 Docker Hub 时，可把基础镜像同步到自带 Registry，或在 Runner 中配置 `registry-mirror`。GitLab 也支持用 Kaniko / Buildah 等无守护进程方案构建，更适合受限环境。

### 8.6 Package Registry

原生支持 npm、Maven、PyPI、NuGet、Composer、Go、Generic 等格式。以 npm 为例：

```bash
npm config set //gitlab.example.com/api/v4/projects/<id>/packages/npm/:_authToken "${CI_JOB_TOKEN}"
npm publish
```

### 8.7 GitLab Pages

最简 Pages 作业：

```yaml
pages:
  stage: deploy
  script:
    - npm run build
  artifacts:
    paths:
      - public
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

访问地址为 `https://<namespace>.gitlab.io/<project>`（自建实例为配置的域名）。注意 `public` 目录名不可更改，且 Pages 有大小与构建时长限制。

### 8.8 Auto DevOps

Auto DevOps 是一套开箱即用的流水线模板：构建 → 测试 → 安全扫描 → 部署到 Kubernetes（或 Review Apps）。适合快速验证，但在成熟团队中通常会被自定义流水线替代——**理解原理后再决定是否使用**。

---

## 9. 安全与合规

### 9.1 权限角色

| 角色 | 典型能力 |
| --- | --- |
| **Guest** | 创建 Issue、评论 |
| **Reporter** | 读取代码、拉取制品、创建代码片段 |
| **Developer** | 推送非保护分支、创建 MR、运行 CI |
| **Maintainer** | 推送保护分支、管理 CI/CD 变量与 Runner、管理项目设置 |
| **Owner** | 转移/删除项目、管理组成员与计费 |

实践原则：**默认授予 Developer，按需提升 Maintainer，Owner 严格控制在 2–3 人**。

### 9.2 受保护分支与标签

- `main`、`release/*`、`production` 全部纳入保护；
- 仅 Maintainer 可合并，Developer 通过 MR 提交；
- 禁止强制推送；
- 对发布标签设置保护，防止误删。

### 9.3 密钥泄露处置

**假设最坏情况：密钥已被推送。**

```bash
# 1. 第一时间在服务方撤销并轮换密钥（优先级最高，永远先做这一步）
# 2. 从当前代码移除
git rm --cached config/secrets.json
echo "config/secrets.json" >> .gitignore
git commit -m "chore: 移除误提交的敏感配置"

# 3. 必要时清洗历史（会改写哈希，团队需重新克隆）
pip install git-filter-repo
git filter-repo --invert-paths --path config/secrets.json
git push --force --all
```

**轮换永远优先于清洗历史**：任何克隆、fork、CI 缓存都可能已留存旧密钥。

预防：`.gitignore` 排除 `.env`、启用**密钥检测（Secret Detection）** 与**推送保护**、CI 用 Variables、提交前 `git diff --staged` 复查。

### 9.4 安全扫描能力

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
  - template: Jobs/Container-Scanning.gitlab-ci.yml
  - template: Jobs/DAST.gitlab-ci.yml
  - template: Jobs/License-Scanning.gitlab-ci.yml
```

| 扫描类型 | 作用 |
| --- | --- |
| SAST | 静态代码漏洞检测 |
| DAST | 对运行中的应用做黑盒探测 |
| Dependency Scanning | 第三方依赖已知漏洞 |
| Container Scanning | 镜像内组件漏洞 |
| Secret Detection | 提交内容中的密钥/口令 |
| License Compliance | 依赖许可证合规性 |
| Coverage Fuzzing | 模糊测试发现崩溃 |

扫描结果统一汇总到 **Secure → Security Dashboard**，并可在 MR 中直接展示新增漏洞，配合「必须修复高危漏洞」的策略形成质量门禁。

> 层级提示：安全扫描类功能多属 Ultimate 层级，且能力随版本演进较快，实施前请核对官方文档。

### 9.5 合规与审计

- **审计事件（Audit Events）**：记录权限变更、设置修改、成员增删等关键操作（付费层级）。
- **合规流水线（Compliance pipeline）**：强制所有项目使用统一的合规作业。
- **合并请求审批规则**：按角色/用户/组设定必需批准人数，可与 CODEOWNERS 联动。
- **推规则（Push rules）**：校验提交信息正则、分支名规范、禁止二进制文件、禁止提交密钥（付费层级）。
- **提交签名**：要求 GPG 或 SSH 签名提交，显示 Verified 标记。

### 9.6 备份与恢复（自建实例）

```bash
# 创建备份（STRATEGY=copy 减少服务阻塞时间）
sudo gitlab-backup create STRATEGY=copy

# 备份目录默认位于 /var/opt/gitlab/backups
# 必须同时备份配置与密钥：
sudo tar czf gitlab-config-$(date +%F).tar.gz /etc/gitlab/gitlab.rb /etc/gitlab/gitlab-secrets.json

# 恢复
sudo gitlab-ctl stop puma
sudo gitlab-ctl stop sidekiq
sudo gitlab-backup restore BACKUP=<时间戳>
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

恢复演练必须定期做——**没有验证过的备份等于没有备份**。

---

## 10. 私有化运维与进阶

### 10.1 日常运维命令

```bash
sudo gitlab-ctl status              # 服务状态
sudo gitlab-ctl reconfigure         # 应用 gitlab.rb 配置变更
sudo gitlab-ctl restart             # 重启全部服务
sudo gitlab-ctl tail                # 跟踪日志
sudo gitlab-ctl tail nginx/gitlab_access.log
sudo gitlab-rake gitlab:check       # 健康检查
sudo gitlab-rake gitlab:env:info    # 环境信息
sudo gitlab-rake gitlab:storage:info  # 存储统计
sudo gitlab-ctl psql                # 进入数据库
sudo gitlab-ctl redis-cli           # 进入 Redis
sudo gitlab-ctl pg-upgrade          # PostgreSQL 大版本升级
```

### 10.2 升级策略

1. 阅读目标版本的 **升级路径说明**（跨大版本通常需要先升到中间版本并运行迁移）。
2. 完整备份（含 `gitlab-secrets.json`）。
3. 在测试环境先行验证。
4. 选择低峰期执行：`apt update && apt install gitlab-ce` → 自动迁移 → `gitlab-ctl reconfigure`。
5. 验证：登录、拉取、跑一条流水线、看日志。

### 10.3 性能与容量

| 症状 | 常见原因 | 处置 |
| --- | --- | --- |
| 页面慢 | Sidekiq 积压 / 数据库压力 | 增加 Sidekiq 并发、优化数据库、增加内存 |
| `git clone` 慢 | 大仓库 / 大文件 | Git LFS、浅克隆、`git gc`、拆分仓库 |
| 流水线排队 | Runner 不足 | 增加 Runner 或启用弹性伸缩 |
| 磁盘快速膨胀 | 制品与镜像堆积 | 设置制品过期、清理 Registry 标签、备份归档 |

监控建议：接入 Prometheus + Grafana（Omnibus 内建 Prometheus 指标端点），关注 Sidekiq 队列延迟、数据库连接数、Gitaly 延迟。

### 10.4 高可用要点

- **Gitaly 集群**：Git 数据层做冗余，避免单点。
- **数据库**：PostgreSQL 主从 + 故障切换。
- **Redis**：哨兵或集群模式。
- **多 Runner**：按 tag 划分资源池，生产 Runner 设为 Protected。
- 自建大规模部署建议参照官方 **Reference Architectures**（按用户规模给出的架构蓝图）。

### 10.5 Git LFS 与大仓库

```bash
git lfs install
git lfs track "*.psd" "*.zip" "*.mp4"
git add .gitattributes
git lfs ls-files
```

自建实例注意调整 Nginx 的 `client_max_body_size` 与 Workhorse 超时，否则大文件上传会以 413/超时失败。

### 10.6 API 与集成

```bash
# REST API
curl --header "PRIVATE-TOKEN: $TOKEN" "https://gitlab.example.com/api/v4/projects"

# GraphQL（复杂查询首选，如批量拉取 MR 与流水线状态）
curl --header "Authorization: Bearer $TOKEN" \
     --header "Content-Type: application/json" \
     --request POST \
     --data '{"query": "{ project(fullPath: \"group/proj\") { name } }"}' \
     "https://gitlab.example.com/api/graphql"
```

**Webhooks**：在项目 Settings → Webhooks 配置推送、MR、Tag、Pipeline 等事件回调，用于通知 IM、触发外部系统。

**GitLab CLI（glab）**：

```bash
glab auth login --hostname gitlab.com
glab repo clone group/project
glab mr create --fill
glab mr list --reviewer=@me
glab mr merge 42 --squash
glab issue create --title "..." --label bug
glab ci status
glab ci trace
glab release create v2.1.0
```

### 10.7 服务器端钩子与推规则

- **Server hooks**（`/var/opt/gitlab/gitlab-data/gitlab-custom-hooks/pre-receive.d/`）：自定义接收前校验，适合强管控场景。
- **Push rules**（Web 界面配置）：提交信息正则、分支命名、文件黑名单、密钥拦截，无需写代码。

---

## 11. 疑难排错手册

### 11.1 流水线显示「This job is stuck」

**原因**：没有匹配的 Runner（标签不匹配、仅限受保护 Runner、Runner 离线）。

**处置**：检查作业的 `tags` 与 Runner 标签是否一致；确认 Runner 状态为 online；如作业在非保护分支上，需有非保护 Runner 接单。

### 11.2 push 被拒：protected branch

```
remote: GitLab: You are not allowed to push code to protected branches on this project.
```

**处置**：改走 MR 流程；如确需直推，请 Maintainer 临时调整保护设置或提权。

### 11.3 流水线没有按预期触发

检查 `workflow:rules` 与作业 `rules` 的组合；常见坑：

- 同一提交同时存在 open 的 MR 时，分支流水线被 `workflow` 规则主动抑制；
- `only: changes` 在推送首个提交时无法可靠判定文件变更，应使用 `rules: changes` 并注意其语义；
- 标签推送需有 `if: $CI_COMMIT_TAG` 规则。

### 11.4 MR 页面显示冲突无法合并

本地处理：`git fetch origin` → `git rebase origin/main` → 解决冲突 → `git push --force-with-lease`。复杂冲突优先本地解决。

### 11.5 作业被杀：exit code 137

通常为**内存不足（OOM）**。处置：换更小基础镜像、限制并发、提高 Runner 宿主机内存、给测试进程设置内存上限。

### 11.6 LFS / 大文件上传失败

现象：`413 Request Entity Too Large` 或超时。处置：调大 `client_max_body_size`、延长 Workhorse 超时、确认 LFS 已启用（`git lfs env`）。

### 11.7 Runner 任务卡在「Preparing」

多为 Docker 执行器拉取镜像失败或 dind 未就绪。处置：配置镜像加速、检查 `DOCKER_TLS_CERTDIR`、查看 `gitlab-runner logs`。

### 11.8 常见错误速查

| 现象 | 原因 | 解决 |
| --- | --- | --- |
| `Permission denied (publickey)` | 公钥未添加 / 用错密钥 | `ssh -T git@gitlab.com` 验证 |
| `Please tell me who you are` | 未配置身份 | `git config user.name/email` |
| `non-fast-forward` | 远程领先 | `git pull --rebase` 后推送 |
| `not a git repository` | 目录不对 | 进入仓库目录或 `git init` |
| `index.lock exists` | 上次命令中断 | 确认无 Git 进程后删除 `.git/index.lock` |
| `403 Forbidden`（API） | Token 权限不足 | 检查 scope 与角色 |
| 流水线成功但未部署 | `rules` 不匹配 | 核对 `$CI_COMMIT_BRANCH` 与 `$CI_DEFAULT_BRANCH` |
| 中文文件名乱码 | quotepath | `git config --global core.quotepath false` |

---

## 12. 工程化最佳实践

### 12.1 仓库初始化检查单

- [ ] `README.md`：一句话定位 + 5 分钟上手
- [ ] `LICENSE`：明确许可
- [ ] `.gitignore` / `.gitattributes`：忽略规则与换行符
- [ ] `CONTRIBUTING.md`：贡献流程与规范
- [ ] `.gitlab/issue_templates/` 与 MR 模板
- [ ] `.gitlab-ci.yml`：至少包含 lint + test
- [ ] 受保护分支：`main` 需 MR + CI 通过
- [ ] CI/CD Variables 已按环境隔离，开启 Protected/Masked
- [ ] 制品过期策略已设定

### 12.2 CI/CD 设计原则

| 原则 | 说明 |
| --- | --- |
| 快反馈 | 测试作业 < 10 分钟，超出则并行拆分 |
| 可复现 | 固定镜像版本，避免 `latest` |
| 幂等 | 同一提交重复执行结果一致 |
| 最小权限 | `CI_JOB_TOKEN` 只读、专用 Deploy Token |
| 左移安全 | lint、密钥检测、SAST 尽量前置到 MR 阶段 |
| 环境等价 | 测试环境与生产尽可能一致 |

### 12.3 Code Review 检查单

- [ ] 改动与 MR 描述一致，范围未扩散
- [ ] 无调试代码、注释掉的代码、硬编码配置
- [ ] 无敏感信息（密钥、内网地址、个人信息）
- [ ] 边界与异常路径已处理
- [ ] 测试充分且「能失败」（先删一行实现验证测试会红）
- [ ] 命名与接口设计清晰，无过度设计
- [ ] 文档 / CHANGELOG 是否需同步
- [ ] 新依赖的许可证与安全状况
- [ ] 性能与并发隐患
- [ ] 向后兼容性与迁移说明

### 12.4 上线前检查单

- [ ] 流水线全绿（含集成/端到端测试）
- [ ] 高危安全漏洞已清零或已评审豁免
- [ ] 版本号按 SemVer 递增，`CHANGELOG.md` 已更新
- [ ] 数据库迁移可回滚
- [ ] 部署审批已完成，避开冻结窗口
- [ ] 监控告警就位，回滚方案已验证

### 12.5 学习路径

| 阶段 | 目标 | 重点 |
| --- | --- | --- |
| 入门（1–2 周） | 独立完成 add/commit/push 与 MR | 三区模型、提交规范、MR 流程 |
| 进阶（1–2 月） | 主导团队协作 | GitLab Flow、评审、冲突、权限 |
| 熟练（3–6 月） | 搭建完整 CI/CD | Runner、`.gitlab-ci.yml`、环境与回滚 |
| 精通（6 月+） | 平台治理与运维 | 安全扫描、合规、备份恢复、性能与高可用 |

---

## 附录 A. 命令速查表

### A.1 Git 核心

| 场景 | 命令 |
| --- | --- |
| 初始化 / 克隆 | `git init` / `git clone <url>` |
| 状态 / 差异 | `git status -sb` / `git diff` / `git diff --staged` |
| 暂存 | `git add -p` |
| 提交 | `git commit -m "msg"` |
| 分支 | `git switch -c <b>` / `git branch -d <b>` |
| 合并 / 变基 | `git merge <b>` / `git rebase <b>` |
| 交互整理 | `git rebase -i HEAD~N` |
| 拉取 / 推送 | `git pull --rebase` / `git push -u origin <b>` |
| 安全强推 | `git push --force-with-lease` |
| 撤销 | `git restore` / `git revert <sha>` / `git reset --hard HEAD~N` |
| 暂存区暂存 | `git stash push -m "msg"` / `git stash pop` |
| 后悔药 | `git reflog` |
| 追溯 | `git blame` / `git log --follow -- <file>` |
| 标签 | `git tag -a v1.0.0 -m "msg"` |

### A.2 GitLab Runner

| 命令 | 作用 |
| --- | --- |
| `gitlab-runner register` | 注册 Runner |
| `gitlab-runner list` | 列出 Runner |
| `gitlab-runner verify` | 校验连通 |
| `gitlab-runner unregister --name <n>` | 注销 |
| `gitlab-runner logs` | 查看日志 |

### A.3 自建实例运维

| 命令 | 作用 |
| --- | --- |
| `gitlab-ctl reconfigure` | 应用配置 |
| `gitlab-ctl status` / `restart` | 状态 / 重启 |
| `gitlab-ctl tail` | 跟踪日志 |
| `gitlab-rake gitlab:check` | 健康检查 |
| `gitlab-backup create` | 创建备份 |
| `gitlab-backup restore BACKUP=<ts>` | 恢复备份 |

### A.4 glab CLI

| 命令 | 作用 |
| --- | --- |
| `glab auth login` | 登录 |
| `glab mr create --fill` | 创建 MR |
| `glab mr merge <id> --squash` | 合并 MR |
| `glab issue create` | 创建 Issue |
| `glab ci status` / `glab ci trace` | 流水线状态 / 日志 |
| `glab release create <tag>` | 创建 Release |

---

## 附录 B. .gitlab-ci.yml 关键字速查

| 关键字 | 作用 |
| --- | --- |
| `workflow:rules` | 控制流水线是否创建 |
| `stages` / `stage` | 阶段定义与归属 |
| `default` | 作业级默认值 |
| `variables` | 变量定义 |
| `image` / `services` | 容器镜像与伴随服务 |
| `before_script` / `script` / `after_script` | 执行脚本 |
| `rules` / `if` / `changes` / `exists` / `when` | 触发条件 |
| `needs` | 跨阶段依赖（DAG） |
| `dependencies` | 指定下载哪些作业的 artifacts |
| `cache` | 缓存配置（key / paths / policy） |
| `artifacts` | 产物（paths / reports / expire_in / when） |
| `environment` | 部署环境（name / url / on_stop / deployment_tier） |
| `when: manual` / `delayed` | 手动 / 延迟执行 |
| `allow_failure` | 失败是否影响流水线 |
| `retry` / `timeout` | 重试与超时 |
| `parallel` / `parallel:matrix` | 并行与矩阵 |
| `tags` | 选择 Runner |
| `trigger` | 触发子/多项目流水线 |
| `include` / `extends` / `!reference` | 复用机制 |
| `release` | 创建 Release |
| `coverage` | 覆盖率正则 |
| `interruptible` | 可被新流水线中断 |
| `resource_group` | 串行化同一资源的并发作业 |

---

## 附录 C. 术语表

| 术语 | 释义 |
| --- | --- |
| Instance | GitLab 实例 |
| Group / Subgroup | 组 / 子组，项目与权限的组织单元 |
| Project | 项目，含仓库与协作对象 |
| Merge Request（MR） | 请求将分支改动合入目标分支 |
| Pipeline | 一次 CI/CD 执行 |
| Stage / Job | 流水线阶段 / 单个作业 |
| Runner | 执行 CI 作业的程序 |
| Executor | Runner 的执行方式（shell/docker/kubernetes） |
| Artifact | 作业产出的文件 |
| Cache | 加速构建的临时缓存 |
| Environment / Deployment | 部署环境 / 一次部署记录 |
| Review App | 随 MR 动态创建的预览环境 |
| Protected Branch | 受保护分支 |
| Protected Variable | 受保护的 CI 变量 |
| Push rule | 服务端推送规则 |
| CODEOWNERS | 代码所有者规则文件 |
| Epic / Milestone / Board | 主题 / 里程碑 / 看板 |
| Quick Action | 评论框中的斜杠命令 |
| Gitaly | Git 仓库读写服务 |
| Omnibus | GitLab 的一体化安装包 |
| Auto DevOps | 内建默认流水线方案 |
| GitLab Flow | GitLab 官方推荐的分支模型 |
| SemVer | 语义化版本号规范 |
| SBOM | 软件物料清单 |

---

## 附录 D. 延伸学习资源

1. **GitLab 官方文档** — [docs.gitlab.com](https://docs.gitlab.com)
   功能、层级与版本的最终依据；订阅层级页与 CI/CD YAML 参考必读。
2. **GitLab Flow 说明** — docs.gitlab.com 上的 *GitLab Flow* 章节
   分支与环境模型的权威定义。
3. **GitLab CI/CD YAML 参考** — docs.gitlab.com 上的 *CI/CD YAML syntax reference*
   全部关键字的语义与版本可用性。
4. **Pro Git（官方书籍，含中文版）** — [git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)
   系统理解 Git 内部原理，第 1、3、7 章建议精读。
5. **Git 官方参考手册** — [git-scm.com/docs](https://git-scm.com/docs)
   命令行为的最终依据。
6. **GitLab University / 官方教程与示例项目** — `gitlab-org/gitlab-foss` 及官方 Sample 项目
   通过真实流水线学习配置写法。
7. **Learn Git Branching** — [learngitbranching.js.org](https://learngitbranching.js.org)
   可视化练习 merge / rebase / cherry-pick。
8. **Conventional Commits** — [conventionalcommits.org](https://www.conventionalcommits.org)
9. **SemVer** — [semver.org](https://semver.org)

---

> **结语**：GitLab 的力量来自「一体化」——Issue、MR、流水线、部署、安全扫描共用一套数据与权限模型。请把这条主线记牢：**小步提交 → MR 评审 → 流水线验证 → 环境部署 → 指标反馈**。工具会迭代、界面会改版、套餐会调整，但「清晰历史、可追溯、可回滚、默认安全」这四条原则，会在任何版本、任何规模的团队里持续生效。
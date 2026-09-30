> **版本**：v1.0　|　**适用对象**：前端工程师、全栈开发者、DevOps 与平台工程人员　|　**阅读方式**：线性阅读 + 按需查阅附录
>
> **阅读须知**：Vercel 平台迭代速度极快（几乎每日部署新特性），本文命令、配置项与 CLI 行为以编写时的稳定版本为准；涉及**计费模型、套餐限额、界面布局**等可能变化的领域，请以 [Vercel 官方文档](https://vercel.com/docs) 为最终依据。文中所有示例代码均可安全复现。

---

## 目录

- [1. 概念基础](#1-概念基础)
- [2. 账号准备与 CLI 安装](#2-账号准备与cli安装)
- [3. 项目部署：从零到线上](#3-项目部署从零到线上)
- [4. 框架深度适配](#4-框架深度适配)
- [5. Serverless Functions](#5-serverless-functions)
- [6. Edge Functions 与 Middleware](#6-edge-functions-与middleware)
- [7. 环境变量与配置管理](#7-环境变量与配置管理)
- [8. 域名、DNS 与证书](#8-域名dns-与证书)
- [9. 预览部署与团队协作](#9-预览部署与团队协作)
- [10. 性能优化与缓存策略](#10-性能优化与缓存策略)
- [11. 监控、分析与可观测性](#11-监控分析与可观测性)
- [12. 安全最佳实践](#12-安全最佳实践)
- [13. CI/CD 与自动化](#13-cicd-与自动化)
- [14. 疑难排错手册](#14-疑难排错手册)
- [15. 工程化最佳实践](#15-工程化最佳实践)
- [附录 A. CLI 命令速查表](#附录-a-cli-命令速查表)
- [附录 B. vercel.json 配置速查](#附录-b-verceljson-配置速查)
- [附录 C. 术语表](#附录-c-术语表)
- [附录 D. 延伸学习资源](#附录-d-延伸学习资源)

---

## 1. 概念基础

### 1.1 Vercel 是什么

Vercel 是一个**前端云平台（Frontend Cloud）**，为现代 Web 应用提供从代码推送、构建、部署、CDN 分发、边缘计算到实时分析的完整链路。它的核心设计理念是 **Zero Config（零配置部署）**：推代码，剩下的交给平台。

Vercel 由 Next.js 的创造者构建，与 Next.js 深度集成，但它同样原生支持 React、Vue、Nuxt、Svelte、SvelteKit、Astro、Remix、Solid、Hono 等 30+ 框架。

### 1.2 架构概览

```
开发者 ──push──▶ Git Provider ──webhook──▶ Vercel Build Pipeline
                                                │
                                     ┌──────────┴──────────┐
                                     ▼                      ▼
                              静态资源（HTML/CSS/JS）    服务端代码
                                     │                      │
                                     ▼                      ▼
                              Vercel CDN              ┌────┴────┐
                              (全球边缘节点)          │ Serverless│
                                                      │ Functions │
                                                      └────┬────┘
                                                           │
                                              ┌────────────┼────────────┐
                                              ▼            ▼            ▼
                                         数据库      第三方 API     存储服务
```

三层核心能力：

| 层 | 组件 | 运行位置 | 冷启动 |
| --- | --- | --- | --- |
| **静态层** | HTML、CSS、JS、图片 | 全球 CDN 边缘节点 | 无 |
| **边缘层** | Edge Functions / Middleware | 边缘节点（基于 V8 隔离） | 极低（<5ms） |
| **计算层** | Serverless Functions | 区域数据中心（Node.js / Python / Go / Ruby） | 较高（100ms–1s） |

理解这三层的适用场景，是用好 Vercel 的关键。

### 1.3 Serverless vs Edge

| 维度 | Serverless Functions | Edge Functions |
| --- | --- | --- |
| 运行时 | Node.js、Python、Go、Ruby | V8（类似浏览器） |
| 冷启动 | 有（100ms–1s） | 极低（<5ms） |
| 执行时间 | 最长 60s（Hobby）/ 300s（Pro） | 最长 30s |
| 内存 | 最大 3GB | 极小（不适合重计算） |
| 包大小 | 最大 250MB | 极小（编译后 < 4MB） |
| Node.js API | 完整 | 仅 Web 标准 API（fetch、Web Crypto 等） |
| 数据库直连 | 可（长连接） | 不可（需 HTTP 驱动） |
| 适用 | 复杂业务逻辑、文件处理、长查询 | 身份验证、A/B 测试、重定向、个性化 |

**选型口诀**：需要 Node.js 原生 API 或数据库长连接 → Serverless；需要低延迟、全球分布、轻量逻辑 → Edge。

### 1.4 核心概念

| 概念 | 说明 |
| --- | --- |
| **Project（项目）** | 一个 Git 仓库对应一个 Vercel 项目 |
| **Deployment（部署）** | 项目的一次构建与发布快照，不可变 |
| **Production** | 默认生产域名对应的部署 |
| **Preview** | Pull Request / 分支自动触发的预览部署 |
| **Instant Rollback** | 将流量瞬间切换到历史部署，无需重新构建 |
| **Domains** | 自定义域名绑定 |
| **Environment Variables** | 按环境（Development / Preview / Production）注入的变量 |
| **Edge Config** | 超低延迟的全球分布式键值存储 |
| **Blob / KV / Postgres / Redis** | Vercel 内建的数据存储服务 |
| **Analytics / Speed Insights** | 内建的流量分析与 Core Web Vitals 监控 |

### 1.5 Vercel 与其他平台的对比

| 维度 | Vercel | Netlify | Cloudflare Pages | AWS (Amplify/CF+S3) |
| --- | --- | --- | --- | --- |
| 框架集成 | 最深（Next.js 原生） | 好 | 好 | 一般 |
| 零配置 | ★★★★★ | ★★★★ | ★★★★ | ★★ |
| Edge Functions | Edge Functions / Middleware | Edge Functions | Workers（极强） | Lambda@Edge |
| 数据库内建 | Postgres/Redis/Blob/KV/Edge Config | 无 | D1/KV/R2 | 多种（但分散） |
| 全球 CDN | ★★★★★ | ★★★★ | ★★★★★ | ★★★★★ |
| 适用场景 | Next.js / 现代前端全栈 | 静态站 + Jamstack | 边缘计算优先 | 企业级 AWS 生态 |

**结论**：用 Next.js 或重视开发者体验 → **Vercel**；边缘计算优先 → Cloudflare；已有 AWS 生态 → AWS。

### 1.6 计费模型

| 套餐 | 典型能力 |
| --- | --- |
| **Hobby** | 个人项目免费，有限制（带宽、函数执行时长、并发数等） |
| **Pro** | 团队项目，更高限额，更长函数超时，更多成员席位 |
| **Enterprise** | 定制化 SLA、SSO、审计、专属支持 |

> **注意**：限额（带宽、函数调用次数、构建分钟数、Edge 请求等）会随政策调整，实施前请查阅官方定价页。

---

## 2. 账号准备与 CLI 安装

### 2.1 注册账号

1. 访问 [vercel.com](https://vercel.com)，支持 GitHub / GitLab / Bitbucket 一键登录。
2. 创建 **Team**（团队）——个人使用可直接用 Default 团队。
3. 绑定 Git Provider 账号，授权仓库访问权限。

### 2.2 安装 Vercel CLI

```bash
# 使用 npm 全局安装（推荐）
npm install -g vercel

# 验证安装
vercel --version

# 登录
vercel login

# 验证登录状态
vercel whoami
```

CLI 是 Vercel 的核心交互工具，覆盖部署、配置、日志、环境变量管理等全部能力。

### 2.3 CLI 鉴权

`vercel login` 会打开浏览器完成 OAuth。CI/CD 环境中使用 **Token**：

```bash
# 在 Settings → Tokens 创建
export VERCEL_TOKEN="your-token-here"

# 或使用 .vercel/auth.json（不推荐提交到仓库）
vercel --token=$VERCEL_TOKEN ...
```

安全守则：Token 绝不提交到代码仓库；CI 中使用 Secrets / Environment Variables 注入；定期轮换。

### 2.4 项目初始化

```bash
# 方式一：在已有项目目录中执行（推荐）
cd my-project
vercel link

# 方式二：克隆 Vercel 模板项目（快速体验）
vercel init
# 交互式选择框架模板，如 Next.js、Nuxt、Astro 等
```

`vercel link` 会在本地创建 `.vercel/project.json`，记录项目 ID 与团队 ID，后续 `vercel` 命令自动关联。

---

## 3. 项目部署：从零到线上

### 3.1 Git 集成部署（推荐）

Vercel 的核心工作流是 **Git-based Deployment**：

1. 在 Vercel Dashboard → **Add New → Project**。
2. 选择 Git Provider，导入仓库。
3. Vercel 自动检测框架，预填构建命令与输出目录（通常无需修改）。
4. 配置环境变量（区分 Preview / Production）。
5. 点击 **Deploy**。

之后每次 `git push` 到主分支自动触发生产部署；每个 Pull Request 自动创建 Preview 部署。

### 3.2 CLI 部署

```bash
# 预览部署（推送到临时 URL）
vercel

# 生产部署
vercel --prod

# 指定项目与团队
vercel --scope my-team

# 部署指定目录（monorepo 场景）
vercel --cwd packages/web

# 跳过确认提示
vercel --yes

# 指定构建命令与输出目录
vercel --build-command "pnpm build" --output-directory "dist"
```

### 3.3 项目设置详解

在 Dashboard → **Project → Settings** 中可配置：

| 设置项 | 说明 |
| --- | --- |
| **Build & Development Settings** | Framework Preset、Build Command、Output Directory、Install Command、Node.js 版本 |
| **Environment Variables** | 按环境（Production / Preview / Development）注入变量 |
| **Domains** | 绑定自定义域名 |
| **Git Integration** | 自动部署、评论、预览 URL 等 |
| **Functions** | 函数区域、内存、最大执行时长 |
| **Security** | 密码保护、DDoS、IP 允许/拒绝、Bot 防护 |
| **Data** | 配置 Edge Config、Blob、KV、Postgres、Redis |

### 3.4 构建配置

Vercel 自动检测框架并预填配置，通常无需手动修改。以下是常见框架的默认值：

| 框架 | Build Command | Output Directory | Node.js 版本 |
| --- | --- | --- | --- |
| Next.js | `next build` | `.next` | ≥ 18.x |
| Vite + React | `npm run build` | `dist` | ≥ 18.x |
| Nuxt | `nuxt build` | `.output/public` | ≥ 18.x |
| Astro | `astro build` | `dist` | ≥ 18.x |
| SvelteKit | `npm run build` | `.svelte-kit/output` | ≥ 18.x |
| Remix | `remix build` | `public`（或 `build`） | ≥ 18.x |
| 纯静态 | 无 | 根目录 / `public` | — |

如需自定义，在 `vercel.json` 或 Dashboard 中覆盖：

```json
{
  "buildCommand": "pnpm run build:prod",
  "outputDirectory": "out",
  "installCommand": "pnpm install --frozen-lockfile"
}
```

### 3.5 monorepo 支持

Vercel 原生支持 Turborepo / Nx / pnpm workspaces / npm workspaces：

```bash
# 从仓库根目录部署指定子项目
vercel --cwd apps/web

# 或在 Dashboard 中设置 Root Directory
# Settings → General → Root Directory → apps/web
```

配合 Turborepo Remote Caching，可显著加速 monorepo 构建：

```bash
turbo login
turbo link
turbo run build
```

---

## 4. 框架深度适配

### 4.1 Next.js（一等公民）

Vercel 对 Next.js 的支持是**原生级别**的，自动启用以下能力：

- **App Router / Pages Router** 全支持。
- **Incremental Static Regeneration (ISR)**：部署后增量重新生成静态页面。
- **Streaming SSR**：React 18 流式渲染。
- **Route Handlers**：API 路由自动映射为 Serverless / Edge Functions。
- **Server Actions**：表单与数据变更的服务器端函数。
- **Image Optimization**：`next/image` 自动接入 Vercel Image Optimization API。
- **Draft Mode**：CMS 预览模式。
- **Turbopack**：更快的本地开发与构建。

```bash
# Next.js 项目部署
npx create-next-app@latest my-app
cd my-app
vercel --prod
```

在 Vercel 上，Next.js 的 `getServerSideProps`、`getStaticProps`、Server Components 会被智能分配到最优运行环境。

> **版本提示**：Next.js 版本迭代快，建议锁定 `next` 与 `react` 的版本号，并在升级前阅读官方 Upgrade Guide。

### 4.2 Vite + React / Vue / Svelte

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
vercel --prod
```

Vite 构建产出纯静态文件，Vercel 自动配置 SPA 路由回退：

```json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

### 4.3 Astro

```bash
npm create astro@latest my-app
cd my-app
vercel --prod
```

Astro 的混合渲染（静态 + 按需 SSR）在 Vercel 上自动工作。使用 `@astrojs/vercel` 适配器：

```bash
npx astro add vercel
```

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';
import vercel from '@astrojs/vercel/serverless';

export default defineConfig({
  output: 'hybrid',
  adapter: vercel(),
});
```

### 4.4 Remix

```bash
npx create-remix@latest my-app
cd my-app
vercel --prod
```

Remix 的 Loader / Action 自动映射为 Serverless Functions。使用官方适配器：

```bash
npm install @remix-run/vercel
```

### 4.5 Hono（轻量边缘框架）

Hono 极适合构建 Edge API：

```typescript
// api/index.ts
import { Hono } from 'hono';
import { handle } from 'hono/vercel';

const app = new Hono().basePath('/api');

app.get('/hello', (c) => {
  return c.json({ message: 'Hello from Edge!' });
});

export const GET = handle(app);
export const POST = handle(app);
```

### 4.6 框架自动检测机制

Vercel 通过 `package.json` 中的依赖自动检测框架：

- 发现 `next` → Next.js
- 发现 `nuxt` → Nuxt
- 发现 `@remix-run/node` → Remix
- 发现 `astro` → Astro
- 发现 `vite` + `react` → Vite (React)
- 无法识别 → 静态站点

手动覆盖：在 `vercel.json` 中指定 `framework`，或在 Dashboard → Build Settings 中选择。

---

## 5. Serverless Functions

### 5.1 文件路由约定

在 `/api` 目录下的文件自动成为 API 端点：

```
project/
├── api/
│   ├── hello.ts          → /api/hello
│   ├── users/
│   │   ├── index.ts      → /api/users
│   │   └── [id].ts       → /api/users/:id
│   └── health.ts         → /api/health
├── public/
└── vercel.json
```

### 5.2 基础函数编写

**TypeScript（Node.js Runtime）**：

```typescript
// api/hello.ts
import type { VercelRequest, VercelResponse } from '@vercel/node';

export default function handler(req: VercelRequest, res: VercelResponse) {
  const { name = 'World' } = req.query;
  res.status(200).json({ message: `Hello, ${name}!` });
}
```

**Python**：

```python
# api/hello.py
from http.server import BaseHTTPRequestHandler
import json

class handler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-Type', 'application/json')
        self.end_headers()
        self.wfile.write(json.dumps({"message": "Hello from Python!"}).encode())
```

**Go**：

```go
// api/hello/main.go
package handler

import (
    "encoding/json"
    "net/http"
)

func Handler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(map[string]string{"message": "Hello from Go!"})
}
```

**Ruby**：

```ruby
# api/hello.rb
require 'json'

class Handler
  def self.call(event:, context:)
    {
      statusCode: 200,
      headers: { 'Content-Type' => 'application/json' },
      body: JSON.generate({ message: 'Hello from Ruby!' })
    }
  end
end
```

### 5.3 请求与响应

```typescript
// api/users/[id].ts
import type { VercelRequest, VercelResponse } from '@vercel/node';

export default async function handler(req: VercelRequest, res: VercelResponse) {
  const { id } = req.query;
  const method = req.method;

  switch (method) {
    case 'GET':
      // 查询单个用户
      const user = await findUser(id as string);
      if (!user) return res.status(404).json({ error: 'User not found' });
      return res.status(200).json(user);

    case 'PUT':
      // 更新用户
      const body = req.body;
      const updated = await updateUser(id as string, body);
      return res.status(200).json(updated);

    case 'DELETE':
      await deleteUser(id as string);
      return res.status(204).end();

    default:
      res.setHeader('Allow', ['GET', 'PUT', 'DELETE']);
      return res.status(405).json({ error: `Method ${method} not allowed` });
  }
}
```

### 5.4 函数配置

在 `vercel.json` 中配置函数的行为：

```json
{
  "functions": {
    "api/heavy-task.ts": {
      "memory": 3008,
      "maxDuration": 60
    },
    "api/quick-check.ts": {
      "maxDuration": 5
    }
  }
}
```

| 配置项 | 说明 | 默认值 |
| --- | --- | --- |
| `memory` | 内存大小（MB） | 1024 |
| `maxDuration` | 最大执行时长（秒） | 10（Hobby）/ 15（Pro）/ 60（自定义） |
| `runtime` | 运行时 | 自动检测（nodejs / python 等） |
| `regions` | 部署区域 | `iad1`（美国东部），支持多区域 |

### 5.5 多区域部署

```json
{
  "functions": {
    "api/*.ts": {
      "regions": ["iad1", "sfo1", "hnd1", "fra1"]
    }
  }
}
```

常用区域代码：

| 代码 | 位置 |
| --- | --- |
| `iad1` | 美国东部（弗吉尼亚） |
| `sfo1` | 美国西部（旧金山） |
| `hnd1` | 亚洲（东京） |
| `sin1` | 亚洲（新加坡） |
| `fra1` | 欧洲（法兰克福） |
| `lhr1` | 欧洲（伦敦） |
| `syd1` | 大洋洲（悉尼） |
| `bom1` | 印度（孟买） |

### 5.6 流式响应（Streaming）

```typescript
// api/stream.ts
import type { VercelRequest, VercelResponse } from '@vercel/node';

export default async function handler(req: VercelRequest, res: VercelResponse) {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');

  const encoder = new TextEncoder();
  const stream = new TransformStream();
  const writer = stream.writable.getWriter();

  // 模拟流式输出
  setInterval(async () => {
    await writer.write(encoder.encode(`data: ${JSON.stringify({ time: Date.now() })}\n\n`));
  }, 1000);

  // 将流发送到客户端
  const reader = stream.readable.getReader();
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    res.write(Buffer.from(value));
  }
}
```

### 5.7 Webhook 处理

```typescript
// api/webhook.ts
import type { VercelRequest, VercelResponse } from '@vercel/node';
import crypto from 'crypto';

export default async function handler(req: VercelRequest, res: VercelResponse) {
  // 验证签名（以 GitHub Webhook 为例）
  const signature = req.headers['x-hub-signature-256'] as string;
  const secret = process.env.WEBHOOK_SECRET!;
  const payload = JSON.stringify(req.body);

  const expected = 'sha256=' + crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  if (signature !== expected) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  // 处理事件
  const event = req.headers['x-github-event'];
  console.log(`Received event: ${event}`);

  // 快速返回 200，避免超时；耗时操作放后台
  res.status(200).json({ received: true });
}
```

---

## 6. Edge Functions 与 Middleware

### 6.1 Edge Functions

Edge Functions 运行在全球边缘节点上，基于 V8 引擎（类似浏览器环境），**不支持完整的 Node.js API**。

**API Route Handler（Edge Runtime）**：

```typescript
// api/geo.ts
export const config = { runtime: 'edge' };

export default function handler(request: Request) {
  const { geo } = request as any; // Vercel 注入的 geo 信息

  return new Response(
    JSON.stringify({
      country: geo?.country,
      city: geo?.city,
      latitude: geo?.latitude,
      longitude: geo?.longitude,
    }),
    {
      headers: { 'Content-Type': 'application/json' },
    }
  );
}
```

**独立 Edge Function**：

```typescript
// edge/hello.ts
export default function handler(request: Request): Response {
  return new Response(
    `<html><body><h1>Hello from Edge!</h1></body></html>`,
    { headers: { 'Content-Type': 'text/html' } }
  );
}
```

### 6.2 Middleware

Middleware 在请求到达页面或 API 之前运行，适合鉴权、重定向、A/B 测试、地理路由等场景。

```typescript
// middleware.ts（项目根目录）
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // 1. 地理路由
  const country = request.geo?.country || 'US';
  const response = NextResponse.next();

  if (country === 'CN') {
    return NextResponse.redirect(new URL('/zh', request.url));
  }

  // 2. 鉴权检查
  const token = request.cookies.get('auth-token');
  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  // 3. 注入自定义 Header
  response.headers.set('x-request-id', crypto.randomUUID());
  response.headers.set('x-geo-country', country);

  return response;
}

// 匹配路径（减少不必要的 Middleware 运行）
export const config = {
  matcher: ['/dashboard/:path*', '/api/protected/:path*', '/'],
};
```

**Middleware 与 Edge Functions 的区别**：

| 维度 | Middleware | Edge Functions |
| --- | --- | --- |
| 触发时机 | 所有匹配请求，页面/ API 之前 | 仅对应 API 路由 |
| 用途 | 鉴权、重定向、Header、A/B | 独立 API 端点 |
| 响应类型 | 可返回 NextResponse / Response | Response |
| 性能影响 | 影响所有匹配请求的延迟 | 仅影响自身路由 |
| 注意事项 | `matcher` 精确匹配，避免全局执行 | 可独立部署 |

### 6.3 地理与设备信息

Vercel 在请求对象中注入丰富的上下文信息：

```typescript
export const config = { runtime: 'edge' };

export default function handler(request: Request) {
  const { geo, ip, headers } = request as any;

  return new Response(JSON.stringify({
    geo: {
      country: geo?.country,
      region: geo?.region,
      city: geo?.city,
      latitude: geo?.latitude,
      longitude: geo?.longitude,
    },
    ip,
    userAgent: headers.get('user-agent'),
    acceptLanguage: headers.get('accept-language'),
  }), {
    headers: { 'Content-Type': 'application/json' }
  });
}
```

### 6.4 Edge Config（低延迟键值存储）

Edge Config 是 Vercel 提供的全球分布式键值存储，读取延迟极低（<1ms），适合特性开关、配置下发等场景。

```typescript
import { get } from '@vercel/edge-config';

export const config = { runtime: 'edge' };

export default async function handler(request: Request) {
  // 读取 Edge Config
  const featureFlags = await get('feature-flags');

  if (featureFlags?.newHomepage) {
    return NextResponse.redirect(new URL('/v2', request.url));
  }

  return NextResponse.next();
}
```

创建 Edge Config：Dashboard → **Storage → Edge Config → Create**，然后通过 `EDGE_CONFIG` 环境变量关联到项目。

### 6.5 Edge Middleware 性能建议

1. **精确 `matcher`**：只匹配需要 Middleware 的路径，避免全局执行。
2. **避免重计算**：Edge 运行时内存小、执行时间短（30s 上限）。
3. **善用 `x-middleware-next`**：不做处理时返回 `NextResponse.next()`。
4. **缓存结果**：用 Edge Config 或 KV 避免重复外部调用。

---

## 7. 环境变量与配置管理

### 7.1 三级环境

| 环境 | 用途 | 变量来源 |
| --- | --- | --- |
| **Development** | 本地开发 | `.env.local` / `.env.development` |
| **Preview** | Pull Request / 分支预览 | Dashboard 或 `vercel env` |
| **Production** | 正式生产环境 | Dashboard 或 `vercel env` |

### 7.2 在 Dashboard 中管理

**Settings → Environment Variables**：

- 选择目标环境（Production / Preview / Development / Preview and Production）。
- 支持加密存储（`Sensitive` 标记后不可回读）。
- 变量在构建阶段和运行时均可使用（可指定 `Build Time` / `Runtime` / `Both`）。

### 7.3 用 CLI 管理

```bash
# 列出所有环境变量
vercel env ls

# 添加变量（交互式）
vercel env add DATABASE_URL
# 提示选择环境：Production / Preview / Development / All

# 添加指定环境
vercel env add API_KEY production

# 读取变量值
vercel env pull .env.local

# 删除变量
vercel env rm DATABASE_URL production

# 从 .env 文件批量导入
vercel env pull .env.production --environment=production
```

### 7.4 本地开发

```bash
# 拉取 Preview / Production 环境变量到本地
vercel env pull .env.local

# 使用本地变量运行开发服务器
vercel dev
```

`.env.local` 不会被提交到 Git（已在 `.gitignore` 中排除），确保安全。

### 7.5 在代码中使用

**Serverless Functions（Node.js）**：

```typescript
export default function handler(req, res) {
  const apiKey = process.env.DATABASE_URL;
  // ...
}
```

**Edge Functions**：

```typescript
export const config = { runtime: 'edge' };

export default function handler(request: Request) {
  // Edge Runtime 中使用 import.meta.env 或 process.env
  const apiKey = process.env.API_KEY;
  // ...
}
```

**前端（构建时注入）**：

```typescript
// 仅暴露 NEXT_PUBLIC_ / VITE_ 前缀的变量到客户端
const apiUrl = process.env.NEXT_PUBLIC_API_URL; // Next.js
const apiUrl = import.meta.env.VITE_API_URL;    // Vite
```

> **安全红线**：任何非 `NEXT_PUBLIC_` / `VITE_` 前缀的变量不会暴露到客户端。**绝不要在前端代码中使用无前缀的变量名来存储密钥。**

### 7.6 环境变量最佳实践

1. **最小权限**：开发用的数据库连接串不要注入生产环境。
2. **敏感标记**：密钥、Token 等标记为 `Sensitive`，防止误泄漏。
3. **前后端分离**：前端用 `NEXT_PUBLIC_` / `VITE_`，后端用无前缀变量。
4. **本地隔离**：开发用 `.env.local`，不与生产共享。
5. **审计与轮换**：定期审查环境变量清单，轮换长期密钥。

---

## 8. 域名、DNS 与证书

### 8.1 添加自定义域名

1. **Dashboard → Project → Settings → Domains → Add**。
2. 输入域名（如 `app.example.com`）。
3. 选择域名用途：
   - **Production**：指向生产部署。
   - **Preview**：预览部署的通配域名（`*.preview.example.com`）。
4. Vercel 提供 DNS 配置指引（通常为 CNAME 或 A 记录）。

### 8.2 DNS 配置

**推荐方式（CNAME）**：

| 记录类型 | 名称 | 值 |
| --- | --- | --- |
| `CNAME` | `app` | `cname.vercel-dns.com` |

**子域名方式（A 记录）**：

| 记录类型 | 名称 | 值 |
| --- | --- | --- |
| `A` | `app` | `76.76.21.21` |

**根域名（Apex）方式**：

| 记录类型 | 名称 | 值 |
| --- | --- | --- |
| `A` | `@` | `76.76.21.21` |

> 根域名无法使用 CNAME（大多数 DNS 服务商不支持），需使用 A 记录。Vercel 提供的 IP `76.76.21.21` 为 Anycast 地址，自动路由到最近节点。

### 8.3 SSL 证书

Vercel **自动为所有绑定的域名签发并续期 Let's Encrypt 证书**，无需手动操作。

高级需求：

- **Wildcard 证书**：需通过 DNS 验证（CNAME TXT 记录），通常在 Enterprise 套餐中支持。
- **自定义证书**：Enterprise 客户可上传自己的证书。

### 8.4 域名重定向与重写

```json
{
  "redirects": [
    {
      "source": "/old-page",
      "destination": "/new-page",
      "permanent": true
    },
    {
      "source": "/blog/:slug",
      "destination": "/articles/:slug",
      "permanent": false
    }
  ],
  "rewrites": [
    {
      "source": "/api/:path*",
      "destination": "/api/handler.ts"
    }
  ],
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "Strict-Transport-Security",
          "value": "max-age=63072000; includeSubDomains"
        }
      ]
    }
  ]
}
```

### 8.5 多域名与子域名路由

```json
{
  "rewrites": [
    {
      "source": "/:path*",
      "has": [
        {
          "type": "host",
          "value": "blog.example.com"
        }
      ],
      "destination": "/blog/:path*"
    }
  ]
}
```

---

## 9. 预览部署与团队协作

### 9.1 预览部署机制

每次 `git push` 到非主分支、或创建 Pull Request 时，Vercel 自动创建一个**唯一 URL** 的预览部署：

```
https://my-app-git-feature-branch-username.vercel.app
```

预览部署包含：

- 完整的构建产物。
- 独立的 Preview 环境变量。
- 部署日志与构建详情。
- **Deployment Preview Comment**：自动在 PR 中留言，附预览链接、截图、性能评分。

### 9.2 预览 URL 分享

- 每个预览部署的 URL 是**永久可访问**的（除非项目删除）。
- 可配置**密码保护**：Settings → Security → Password Protection。
- 可配置 **Vercel Authentication**：仅团队成员可访问预览。

### 9.3 Instant Rollback

一键将生产流量切换到任意历史部署：

1. Dashboard → **Deployments**。
2. 找到目标历史部署。
3. 点击 **Promote to Production**（或 **Instant Rollback**）。

**无需重新构建**——历史部署的产物已存储，切换只是指针移动，通常在数秒内生效。

### 9.4 团队协作

| 功能 | 说明 |
| --- | --- |
| **Comments** | 在预览部署页面标注 UI 问题 |
| **Deployment Protection** | 密码保护 / Vercel Auth / IP 白名单 |
| **Project Roles** | Owner / Member / Viewer |
| **Team Analytics** | 团队级别的流量与性能概览 |
| **Concurrent Builds** | Pro 套餐支持多项目同时构建 |

### 9.5 Git 集成增强

在 **Settings → Git** 中配置：

- **Deploy Hooks**：外部系统触发部署的 Webhook URL。
- **Ignored Build Step**：仅当指定路径变更时才触发构建（monorepo 必备）。
- **Connected Repositories**：一个项目可关联多个仓库。
- **Commit Comments**：部署状态自动标注到 commit。

```bash
# Deploy Hook 示例：外部 CMS 更新后触发重新构建
curl -X POST "https://api.vercel.com/v1/integrations/deploy/HOOK_ID"
```

---

## 10. 性能优化与缓存策略

### 10.1 CDN 与缓存

Vercel 的静态资源自动通过全球 CDN 分发，默认缓存策略：

| 资源类型 | 缓存行为 |
| --- | --- |
| 带 hash 的静态文件（JS/CSS/图片） | `Cache-Control: public, max-age=31536000, immutable` |
| HTML 文件 | 不缓存或短缓存（确保及时更新） |
| Serverless Function 响应 | 默认不缓存（可通过 `Cache-Control` 自定义） |

自定义缓存头：

```json
{
  "headers": [
    {
      "source": "/static/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    },
    {
      "source": "/api/feed",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "s-maxage=60, stale-while-revalidate=300"
        }
      ]
    }
  ]
}
```

### 10.2 ISR（增量静态再生）

Next.js 的 ISR 在 Vercel 上是**零配置可用**的：

```typescript
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetchPosts();
  return posts.map((post) => ({ slug: post.slug }));
}

export default async function BlogPost({ params }) {
  const post = await fetchPost(params.slug);
  // ...
}

// 指定再生成间隔
export const revalidate = 60; // 60 秒后重新生成
```

在 Vercel 上，ISR 使用 **Tag-based Revalidation**，可以精确失效特定页面：

```typescript
import { revalidateTag } from 'next/cache';

export async function POST(request: Request) {
  const { slug } = await request.json();
  revalidateTag(`post-${slug}`);
  return Response.json({ revalidated: true });
}
```

### 10.3 Image Optimization

Vercel 内建图片优化，自动进行格式转换（WebP / AVIF）、尺寸裁剪、质量压缩：

```typescript
// Next.js
import Image from 'next/image';

export default function Avatar() {
  return (
    <Image
      src="/avatar.jpg"
      alt="Avatar"
      width={200}
      height={200}
      priority
      placeholder="blur"
    />
  );
}
```

配置图片域名白名单（`next.config.js`）：

```js
module.exports = {
  images: {
    remotePatterns: [
      { protocol: 'https', hostname: 'cdn.example.com' },
      { protocol: 'https', hostname: 'images.unsplash.com' },
    ],
  },
};
```

### 10.4 字体优化

Next.js 的 `next/font` 在 Vercel 上自动内联字体文件，消除 FOIT/FOUT：

```typescript
import { Inter, JetBrains_Mono } from 'next/font/google';

const inter = Inter({ subsets: ['latin'], display: 'swap' });
const mono = JetBrains_Mono({ subsets: ['latin'], display: 'swap' });

export default function RootLayout({ children }) {
  return (
    <html lang="zh-CN" className={inter.className}>
      <body>
        {children}
      </body>
    </html>
  );
}
```

### 10.5 构建优化

```bash
# Turborepo Remote Caching（monorepo）
turbo login
turbo link

# Next.js Turbopack（开发模式）
npx next dev --turbo

# 固定 Node.js 版本（避免构建不一致）
# Settings → Node.js Version → 20.x
```

在 `vercel.json` 中启用构建缓存：

```json
{
  "buildCommand": "turbo run build",
  "installCommand": "pnpm install --frozen-lockfile"
}
```

### 10.6 性能清单

- [ ] 静态资源带 hash 且使用 `immutable` 缓存
- [ ] HTML 页面使用 `stale-while-revalidate`
- [ ] ISR 精确设置 `revalidate` 间隔
- [ ] 图片使用 `next/image` 或 `<picture>` + WebP/AVIF
- [ ] 字体内联，`display: swap`
- [ ] JS Bundle 通过 `dynamic import` 代码分割
- [ ] 函数响应开启 `Cache-Control`
- [ ] Edge Config 下发配置，避免重复调用数据库

---

## 11. 监控、分析与可观测性

### 11.1 Vercel Analytics

**Dashboard → Analytics**：

- **Web Analytics**：页面浏览量、唯一访客、流量来源、地理分布。
- **Speed Insights**：Core Web Vitals（LCP、FID/INP、CLS）真实用户数据。

安装（Next.js）：

```bash
npm install @vercel/analytics
```

```typescript
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react';
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
        <SpeedInsights />
      </body>
    </html>
  );
}
```

### 11.2 日志查看

**Dashboard → Deployments → [选择部署] → Functions**：

- 实时函数日志（stdout / stderr）。
- 按函数、按请求过滤。
- 查看响应时间、内存使用。

CLI 查看日志：

```bash
# 查看最新生产部署日志
vercel logs

# 查看指定部署日志
vercel logs --url https://my-app.vercel.app

# 实时跟踪日志
vercel logs --follow

# 按函数过滤
vercel logs --query "path=/api/users"
```

### 11.3 第三方监控集成

```typescript
// Sentry 示例
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: 0.1,
});

export default function handleError(error: Error) {
  Sentry.captureException(error);
}
```

常用集成：

| 工具 | 用途 |
| --- | --- |
| Sentry | 错误追踪与性能监控 |
| Datadog / New Relic | APM 与基础设施监控 |
| LogRocket | 前端会话回放 |
| PostHog | 产品分析与特性开关 |
| Axiom | 日志聚合与查询 |

### 11.4 健康检查

```typescript
// api/health.ts
export default async function handler(req, res) {
  const checks: Record<string, string> = {};

  // 数据库连接
  try {
    await db.query('SELECT 1');
    checks.database = 'healthy';
  } catch {
    checks.database = 'unhealthy';
  }

  // 外部依赖
  try {
    await fetch('https://api.external.com/ping');
    checks.externalApi = 'healthy';
  } catch {
    checks.externalApi = 'unhealthy';
  }

  const healthy = Object.values(checks).every((v) => v === 'healthy');

  res.status(healthy ? 200 : 503).json({
    status: healthy ? 'healthy' : 'degraded',
    checks,
    timestamp: new Date().toISOString(),
  });
}
```

---

## 12. 安全最佳实践

### 12.1 安全头

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        },
        {
          "key": "Referrer-Policy",
          "value": "strict-origin-when-cross-origin"
        },
        {
          "key": "Permissions-Policy",
          "value": "camera=(), microphone=(), geolocation=()"
        },
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; connect-src 'self' https://api.example.com"
        },
        {
          "key": "Strict-Transport-Security",
          "value": "max-age=63072000; includeSubDomains; preload"
        }
      ]
    }
  ]
}
```

### 12.2 CORS 配置

在 API 函数中手动设置：

```typescript
export default function handler(req, res) {
  // 允许的来源
  const allowedOrigins = ['https://app.example.com', 'https://admin.example.com'];
  const origin = req.headers.origin;

  if (origin && allowedOrigins.includes(origin)) {
    res.setHeader('Access-Control-Allow-Origin', origin);
    res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
    res.setHeader('Access-Control-Allow-Credentials', 'true');
    res.setHeader('Vary', 'Origin');
  }

  // 预检请求
  if (req.method === 'OPTIONS') {
    return res.status(204).end();
  }

  // 业务逻辑
  res.status(200).json({ message: 'OK' });
}
```

### 12.3 身份验证

**推荐方案**（按场景选择）：

| 方案 | 适用 | 说明 |
| --- | --- | --- |
| **Vercel Authentication** | 内部工具、预览环境 | 零代码集成，仅限团队成员 |
| **Auth.js (NextAuth)** | 全栈应用 | 开源、多提供商、灵活 |
| **Clerk** | 快速集成 | 托管式用户管理 |
| **Supabase Auth** | Supabase 生态 | Postgres RLS + Auth 一体 |
| **自定义 JWT** | 高度定制 | 需自行实现刷新、吊销 |

```typescript
// middleware.ts - 鉴权守卫
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const token = request.cookies.get('session')?.value;

  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*'],
};
```

### 12.4 Rate Limiting

Vercel 本身不提供内建限流（截至编写时），需在应用层或第三方实现：

```typescript
// api/limited.ts - 简易内存限流（示例，生产建议用 Redis）
const rateLimit = new Map<string, { count: number; resetTime: number }>();

export default function handler(req, res) {
  const ip = req.headers['x-forwarded-for'] || req.socket.remoteAddress;
  const now = Date.now();
  const windowMs = 60 * 1000; // 1 分钟
  const maxRequests = 60;

  const record = rateLimit.get(ip as string);
  if (record && now < record.resetTime) {
    if (record.count >= maxRequests) {
      return res.status(429).json({ error: 'Too Many Requests' });
    }
    record.count++;
  } else {
    rateLimit.set(ip as string, { count: 1, resetTime: now + windowMs });
  }

  res.status(200).json({ message: 'OK' });
}
```

生产环境推荐使用 Upstash Redis 或 Vercel KV 实现分布式限流。

### 12.5 密钥保护

1. 密钥存放于 **Environment Variables**，标记为 `Sensitive`。
2. 不要在代码中硬编码密钥。
3. 不要在客户端代码中暴露后端密钥。
4. 定期轮换 API Key 与 Token。
5. 使用 Vercel 的 **Secret Scanning** 自动检测泄露（部分套餐支持）。

### 12.6 DDoS 与 Bot 防护

Vercel 内建基础 DDoS 防护。高级需求：

- **Vercel WAF**（Enterprise）：自定义规则、IP 黑名单。
- **Cloudflare 代理**：在 Vercel 前加 Cloudflare，双重防护。
- **Bot Detection**：`vercel.json` 中配置 IP 允许/拒绝：

```json
{
  "crons": [],
  "firewall": {
    "ipRules": [
      { "ip": "192.168.1.0/24", "action": "allow" },
      { "ip": "10.0.0.1", "action": "deny" }
    ]
  }
}
```

> 注：`firewall` 配置项在不同套餐中可用性不同，请以官方文档为准。

---

## 13. CI/CD 与自动化

### 13.1 Vercel 内建的 CI/CD

Vercel 的 Git 集成**本身就是 CI/CD**：

| 触发 | 动作 |
| --- | --- |
| `git push` 到主分支 | 自动构建 + 部署到生产 |
| `git push` 到非主分支 | 自动构建 + 部署到预览 |
| 创建 Pull Request | 自动构建 + 部署到预览 + PR 评论 |
| 合并 Pull Request | 自动部署到生产 |

### 13.2 自定义构建流程

在 `vercel.json` 中定义构建命令：

```json
{
  "buildCommand": "npm run lint && npm run test:ci && npm run build",
  "installCommand": "npm ci",
  "outputDirectory": "dist"
}
```

在 `package.json` 中定义完整脚本：

```json
{
  "scripts": {
    "lint": "eslint . --max-warnings 0",
    "test:ci": "vitest run --coverage",
    "build": "next build",
    "type-check": "tsc --noEmit"
  }
}
```

### 13.3 使用 GitHub Actions 增强

对于需要额外步骤（如 E2E 测试、安全扫描）的场景：

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run test:ci

  e2e:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - name: Get Preview URL
        id: preview
        run: |
          echo "url=https://my-app-git-${GITHUB_HEAD_REF}-username.vercel.app" >> $GITHUB_OUTPUT
      - run: npx playwright test
        env:
          BASE_URL: ${{ steps.preview.outputs.url }}
```

### 13.4 Deploy Hooks

外部系统触发部署：

```bash
# 在 Dashboard → Settings → Deploy Hooks 创建
# 然后使用 curl 或代码触发
curl -X POST "https://api.vercel.com/v1/integrations/deploy/HOOK_ID"
```

### 13.5 Monorepo 构建优化

使用 Turborepo Remote Caching 加速 CI：

```bash
# turbo.json
{
  "remoteCache": {
    "signature": true
  }
}
```

```bash
# 在 CI 中设置
export TURBO_TOKEN="your-token"
export TURBO_TEAM="your-team"
turbo run build --remote-only
```

### 13.6 预览 URL 获取（脚本化）

```bash
# 使用 Vercel CLI 获取部署 URL
vercel --prod --yes
# 输出: https://my-app-abc123.vercel.app

# 使用 API 获取
curl -H "Authorization: Bearer $VERCEL_TOKEN" \
  "https://api.vercel.com/v6/deployments?projectId=$PROJECT_ID&target=production&limit=1"
# 响应中的 deployments[0].url 即为最新部署 URL
```

---

## 14. 疑难排错手册

### 14.1 构建失败

| 症状 | 原因 | 解决 |
| --- | --- | --- |
| `Module not found` | 依赖缺失 / 路径错误 | 检查 `package.json` 与 `tsconfig.json` 路径 |
| `Out of memory` | 构建内存不足 | 拆分构建、减少 bundle、升级套餐 |
| `Build timeout` | 构建超时 | 优化构建速度、使用缓存、拆分 monorepo |
| `Syntax error` | Node.js 版本不兼容 | 在设置中指定 Node.js 版本 |
| `Permission denied` | 安装脚本权限 | 检查 `installCommand` 与依赖版本 |

### 14.2 函数超时

```
504 Gateway Timeout
```

- 检查 `maxDuration` 配置（`vercel.json` → `functions`）。
- 检查是否进行了同步阻塞操作（如大文件读取）。
- 检查外部 API 响应时间。
- 升级套餐以获得更长执行时长。

### 14.3 预览部署显示旧代码

1. 检查分支名是否正确推送到远程。
2. 检查 Ignored Build Step 是否误跳过了构建。
3. 清除浏览器缓存（静态资源可能被缓存）。
4. 在 Dashboard 中检查部署列表，确认是否有新部署生成。

### 14.4 环境变量未生效

1. 确认变量添加到了正确的环境（Production / Preview / Development）。
2. 修改环境变量后**需要重新部署**才生效（仅运行时变量可在下次函数调用时使用）。
3. 前端变量必须有 `NEXT_PUBLIC_` / `VITE_` 前缀。
4. 检查变量名拼写（大小写敏感）。

### 14.5 自定义域名不生效

1. 等待 DNS 传播（最长 48 小时，通常 5–30 分钟）。
2. 检查 CNAME / A 记录值是否正确。
3. 检查域名是否已验证（Dashboard → Domains）。
4. 根域名必须用 A 记录（`76.76.21.21`），不能用 CNAME。

### 14.6 Middleware 意外触发

- 确保 `config.matcher` 精确匹配目标路径。
- 使用正则排除静态资源：`matcher: ['/((?!_next/static|_next/image|favicon.ico).*)']`。

### 14.7 图片优化报错

```
Error: The requested resource isn't a valid image
```

- 确认远程图片域名已加入 `next.config.js` 的 `remotePatterns`。
- 确认图片 URL 可访问（非 404）。
- 确认图片格式受支持（JPEG、PNG、WebP、AVIF、GIF）。

### 14.8 常见 HTTP 错误速查

| 错误码 | 含义 | 常见原因 |
| --- | --- | --- |
| `404` | 页面不存在 | 路由配置错误、文件缺失 |
| `500` | 服务器内部错误 | 函数异常、环境变量缺失 |
| `502` | 网关错误 | 函数崩溃、冷启动超时 |
| `504` | 网关超时 | 函数执行超时 |
| `429` | 请求过多 | 超出配额、限流触发 |
| `DEPLOYMENT_NOT_FOUND` | 部署不存在 | URL 错误、部署已删除 |
| `FUNCTION_INVOCATION_TIMEOUT` | 函数超时 | `maxDuration` 不足 |
| `FUNCTION_INVOCATION_FAILED` | 函数失败 | 代码异常、依赖缺失 |
| `DEPLOYMENT_DISABLED` | 部署已禁用 | 手动禁用、账户问题 |
| `EDGE_FUNCTION_INVOCATION_TIMEOUT` | Edge 函数超时 | Edge 执行超 30s |

---

## 15. 工程化最佳实践

### 15.1 项目初始化检查单

- [ ] `README.md`：一句话定位 + 5 分钟上手
- [ ] `LICENSE`：明确许可
- [ ] `.gitignore`：排除 `.env.local`、`.vercel/`、`node_modules/`
- [ ] `vercel.json`：配置构建命令、缓存头、安全头
- [ ] CI 流水线：至少包含 lint + type-check + test
- [ ] 环境变量：三级隔离，敏感值标记 Sensitive
- [ ] 自定义域名 + SSL：已配置并验证
- [ ] 监控：Analytics + Speed Insights 已接入
- [ ] 安全头：CSP、HSTS、X-Frame-Options 等已配置

### 15.2 部署流程最佳实践

| 环节 | 做法 |
| --- | --- |
| 提交 | 小步提交、规范信息（Conventional Commits） |
| 构建 | 锁定 Node.js 版本、使用 `npm ci` |
| 测试 | lint + type-check + unit test + e2e test |
| 预览 | 每个 PR 自动预览，人工验证后合并 |
| 生产 | 合并主分支自动部署 |
| 回滚 | 使用 Instant Rollback，不重新构建 |
| 监控 | Speed Insights 持续跟踪 Core Web Vitals |

### 15.3 安全检查单

- [ ] 安全头（CSP、HSTS、X-Frame-Options 等）已配置
- [ ] CORS 只允许必要来源
- [ ] 密钥存储于 Environment Variables，未硬编码
- [ ] API 端点有鉴权（Middleware 或函数内检查）
- [ ] Rate Limiting 已实现
- [ ] 依赖定期更新（Dependabot / Renovate）
- [ ] 预览环境启用密码保护（内部项目）

### 15.4 性能检查单

- [ ] 静态资源带 hash 且 `immutable` 缓存
- [ ] 图片使用 `next/image` 或优化后格式
- [ ] 字体内联，`display: swap`
- [ ] ISR 合理设置 `revalidate`
- [ ] JS Bundle < 200KB（gzip 后）
- [ ] Core Web Vitals 均达标（LCP < 2.5s、INP < 200ms、CLS < 0.1）

### 15.5 成本优化

| 策略 | 说明 |
| --- | --- |
| 启用 ISR | 减少 Serverless Function 调用 |
| 合理设置缓存 | 减少源站请求 |
| 优化构建时间 | 使用 Turborepo Cache、固定依赖版本 |
| 控制函数内存 | 按需分配，不要默认 3GB |
| 清理旧部署 | 定期删除不需要的预览部署 |
| 监控用量 | Dashboard → Usage 查看实时消耗 |

### 15.6 学习路径

| 阶段 | 目标 | 重点 |
| --- | --- | --- |
| 入门（1–2 天） | 部署一个静态站点 | CLI、Git 集成、自定义域名 |
| 进阶（1–2 周） | 构建全栈应用 | Serverless Functions、环境变量、预览部署 |
| 熟练（1–2 月） | 优化与监控 | Edge Functions、Middleware、ISR、Analytics |
| 精通（3 月+） | 平台治理 | 安全策略、CI/CD 自动化、多项目架构、成本优化 |

---

## 附录 A. CLI 命令速查表

| 命令 | 作用 |
| --- | --- |
| `vercel login` | 登录 |
| `vercel whoami` | 查看当前用户 |
| `vercel link` | 关联本地项目与远程项目 |
| `vercel` | 创建预览部署 |
| `vercel --prod` | 创建生产部署 |
| `vercel --yes` | 跳过交互式确认 |
| `vercel --cwd <dir>` | 指定工作目录 |
| `vercel --scope <team>` | 指定团队 |
| `vercel --token <t>` | 使用指定 Token |
| `vercel dev` | 本地开发服务器 |
| `vercel build` | 本地构建（不部署） |
| `vercel logs` | 查看函数日志 |
| `vercel logs --follow` | 实时跟踪日志 |
| `vercel env ls` | 列出环境变量 |
| `vercel env add <name>` | 添加环境变量 |
| `vercel env rm <name> <env>` | 删除环境变量 |
| `vercel env pull <file>` | 拉取环境变量到本地文件 |
| `vercel domains add <domain>` | 添加域名 |
| `vercel domains ls` | 列出域名 |
| `vercel projects ls` | 列出项目 |
| `vercel inspect <url>` | 查看部署详情 |
| `vercel promote <url>` | 提升预览部署到生产 |
| `vercel rollback [url]` | 回滚到指定部署 |
| `vercel remove <project>` | 移除项目 |
| `vercel init` | 从模板创建项目 |
| `vercel --version` | 查看 CLI 版本 |

---

## 附录 B. vercel.json 配置速查

| 配置项 | 作用 | 示例 |
| --- | --- | --- |
| `buildCommand` | 自定义构建命令 | `"npm run build"` |
| `installCommand` | 自定义安装命令 | `"npm ci"` |
| `outputDirectory` | 构建输出目录 | `"dist"` |
| `framework` | 手动指定框架 | `"nextjs"` |
| `devCommand` | 本地开发命令 | `"npm run dev"` |
| `cleanUrls` | 移除 URL 中的 `.html` | `true` |
| `trailingSlash` | 是否保留尾部斜杠 | `false` |
| `rewrites` | URL 重写规则 | `[{"source":"/api/:path*","destination":"/api/handler.ts"}]` |
| `redirects` | URL 重定向规则 | `[{"source":"/old","destination":"/new","permanent":true}]` |
| `headers` | 自定义 HTTP 头 | 见安全头配置 |
| `functions` | 函数级配置 | `{"api/h.ts":{"memory":1024,"maxDuration":30}}` |
| `crons` | 定时任务 | `[{"path":"/api/cron","schedule":"0 * * * *"}]` |
| `regions` | 默认部署区域 | `["iad1","hnd1"]` |
| `env` | 构建时变量 | `{"MY_VAR":"value"}` |
| `build.env` | 构建专用变量 | `{"NODE_ENV":"production"}` |
| `dataCache` | 数据缓存配置 | 见 ISR 相关文档 |
| `firewall` | IP 允许/拒绝 | 见安全章节 |
| `images` | 图片优化配置 | `{"sizes":[640,1024],"formats":["image/webp"]}` |
| `preventBlocking` | 阻止第三方脚本阻塞渲染 | `true` |
| `skipGitHubConnectDuringLink` | CLI 跳过 GitHub 连接 | `true` |
| `installCommand` | 自定义安装命令 | `"pnpm install --frozen-lockfile"` |
| `outputDirectory` | 输出目录 | `"dist"` |
| `cleanUrls` | 清理 URL 后缀 | `true` |

---

## 附录 C. 术语表

| 术语 | 释义 |
| --- | --- |
| **Deployment** | 项目的一次构建与发布快照，不可变 |
| **Preview Deployment** | 非主分支 / PR 触发的预览环境 |
| **Production** | 生产环境的当前活跃部署 |
| **Instant Rollback** | 将流量切换到历史部署，无需重新构建 |
| **Serverless Function** | 按需执行的服务端函数（Node.js / Python / Go / Ruby） |
| **Edge Function** | 运行在边缘节点的轻量函数（V8 运行时） |
| **Middleware** | 在请求到达页面/API 之前运行的拦截器 |
| **Edge Config** | 超低延迟的全球分布式键值存储 |
| **ISR** | 增量静态再生（Incremental Static Regeneration） |
| **CDN** | 内容分发网络（Content Delivery Network） |
| **Build Cache** | 构建过程中可复用的缓存产物 |
| **Deploy Hook** | 外部触发部署的 Webhook URL |
| **Ignored Build Step** | 控制是否跳过构建的脚本 |
| **Environment Variable** | 按环境注入的配置变量 |
| **Project** | 一个 Git 仓库对应的 Vercel 项目 |
| **Team** | 团队工作空间 |
| **Speed Insights** | 真实用户 Core Web Vitals 监控 |
| **Blob Storage** | Vercel 对象存储 |
| **KV Storage** | Vercel 键值存储 |
| **Vercel Postgres** | Vercel 托管 PostgreSQL |
| **Vercel Redis** | Vercel 托管 Redis |
| **Turbopack** | Rust 编写的快速构建工具 |
| **Streaming SSR** | React 18 流式服务端渲染 |
| **Zero Config** | 零配置部署理念 |
| **Cron Job** | 定时任务 |

---

## 附录 D. 延伸学习资源

1. **Vercel 官方文档** — [vercel.com/docs](https://vercel.com/docs)
   平台功能、CLI 参考、框架指南的最终依据。
2. **Vercel 博客** — [vercel.com/blog](https://vercel.com/blog)
   新特性发布、性能优化、工程实践分享。
3. **Next.js 官方文档** — [nextjs.org/docs](https://nextjs.org/docs)
   Next.js 在 Vercel 上是一等公民，理解 Next.js 等于理解 Vercel 的核心能力。
4. **Vercel Templates** — [vercel.com/templates](https://vercel.com/templates)
   精选项目模板，一键部署体验。
5. **Turborepo 文档** — [turbo.build](https://turbo.build)
   Monorepo 构建优化与 Remote Caching。
6. **Web Vitals 指南** — [web.dev/vitals](https://web.dev/vitals)
   Core Web Vitals 的权威定义与优化方法。
7. **MDN Web Docs** — [developer.mozilla.org](https://developer.mozilla.org)
   Web 标准 API 参考（Edge Functions 基于 Web 标准）。
8. **Hono 文档** — [hono.dev](https://hono.dev)
   轻量边缘框架，适合构建 Vercel Edge Functions。
9. **Auth.js (NextAuth)** — [authjs.dev](https://authjs.dev)
   全栈应用身份验证方案。

---

> **结语**：Vercel 的价值不在于「又一个部署平台」，而在于它把**构建、部署、分发、计算、监控**统一到一条极简的开发者体验链路中。请把核心心法记住：**Git 是唯一的部署入口；不可变部署是安全的基石；边缘优先是性能的方向；零配置是效率的来源。** 工具会迭代、套餐会变化、界面会改版，但「推送即部署、一键即回滚、全球即分发」这三条原则，会在任何现代 Web 项目中持续生效。
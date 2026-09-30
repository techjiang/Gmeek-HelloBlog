# 

> **版本**：v1.0　|　**适用对象**：零基础到中高级前端工程师　|　**阅读方式**：线性阅读 + 按需查阅附录
>
> **阅读须知**：本文以 **Vue 3（3.4+）** 为主线，使用 **`<script setup>` 语法糖 + Composition API + TypeScript** 作为推荐范式。Vue 2 已于 2023 年 12 月 31 日结束维护（EOL），新项目请直接使用 Vue 3。Vue 核心 API 行为稳定，文中代码可长期复用；涉及**生态库版本、构建工具配置**等会随迭代变化的领域，请以 [Vue 官方文档](https://vuejs.org) 最新说明为准。

---

## 目录

- [1. Vue 是什么](#1-vue-是什么)
- [2. 环境搭建与项目创建](#2-环境搭建与项目创建)
- [3. 模板语法与指令](#3-模板语法与指令)
- [4. 响应式系统](#4-响应式系统)
- [5. 组件化开发](#5-组件化开发)
- [6. 组合式 API 深入](#6-组合式-api-深入)
- [7. 生命周期](#7-生命周期)
- [8. 表单处理与双向绑定](#8-表单处理与双向绑定)
- [9. 过渡与动画](#9-过渡与动画)
- [10. 自定义指令](#10-自定义指令)
- [11. 插件与全局配置](#11-插件与全局配置)
- [12. Vue Router 路由](#12-vue-router-路由)
- [13. Pinia 状态管理](#13-pinia-状态管理)
- [14. TypeScript 集成](#14-typescript-集成)
- [15. 性能优化](#15-性能优化)
- [16. 测试](#16-测试)
- [17. SSR 与 Nuxt](#17-ssr-与-nuxt)
- [18. 工程化最佳实践](#18-工程化最佳实践)
- [19. 疑难排错手册](#19-疑难排错手册)
- [附录 A. API 速查表](#附录-a-api-速查表)
- [附录 B. 术语表](#附录-b-术语表)
- [附录 C. 延伸学习资源](#附录-c-延伸学习资源)

---

## 1. Vue 是什么

### 1.1 定位与理念

Vue 是一个用于构建用户界面的 **渐进式 JavaScript 框架**。核心理念是：

- **声明式渲染**：用模板描述「UI 应该长什么样」，框架负责高效更新。
- **组件化**：将页面拆分为独立、可复用的组件单元。
- **响应式数据驱动**：数据变化时，视图自动更新；无需手动操作 DOM。
- **渐进式采用**：可以只用核心库做简单的页面增强，也可以搭配路由、状态管理、构建工具构建完整的 SPA / SSR 应用。

### 1.2 Vue 3 的核心优势

| 能力 | 说明 |
| --- | --- |
| **Composition API** | 按逻辑关注点组织代码，告别 Options API 的「横切关注点」问题 |
| **`<script setup>`** | 更简洁的单文件组件语法糖，编译期优化 |
| **Proxy 响应式** | 更完整的响应式追踪（支持 Map/Set/数组索引/对象新增属性） |
| **Teleport** | 将子组件渲染到 DOM 中的任意位置（弹窗、通知等场景） |
| **Suspense** | 异步组件的优雅加载状态管理 |
| **Fragment** | 组件可以有多个根节点 |
| **v-model 增强** | 支持参数化 `v-model`（`v-model:title`） |
| **自定义渲染器** | 可扩展到 WebGL、终端、小程序等非浏览器平台 |
| **RFC 驱动演进** | 功能决策公开透明 |

### 1.3 Vue 2 与 Vue 3 的关键差异

| 维度 | Vue 2 | Vue 3 |
| --- | --- | --- |
| 响应式原理 | `Object.defineProperty` | `Proxy` |
| API 风格 | Options API | Composition API + Options API |
| 组件根节点 | 必须单根 | 支持多根（Fragment） |
| 生命周期 | `beforeCreate` / `created` 等 | `setup()` / `onMounted` 等 |
| 全局 API | `Vue.xxx` | `app.xxx`（应用实例） |
| `v-model` | 单个，默认 `value` + `input` | 可参数化，默认 `modelValue` + `update:modelValue` |
| 构建工具 | webpack | **Vite（官方推荐）** |
| TypeScript | 支持一般 | 原生深度支持 |
| 状态管理 | Vuex | **Pinia（官方推荐）** |

> **迁移建议**：新项目直接用 Vue 3。Vue 2 → 3 迁移可参考官方 [Migration Guide](https://v3-migration.vuejs.org)，使用 `@vue/compat` 兼容构建逐步迁移。

### 1.4 生态全景

| 领域 | 推荐工具 |
| --- | --- |
| 构建工具 | **Vite**（官方） |
| 路由 | **Vue Router 4**（官方） |
| 状态管理 | **Pinia**（官方） |
| UI 组件库 | Element Plus / Ant Design Vue / Naive UI / Vuetify / shadcn-vue |
| 测试 | Vitest + Vue Test Utils + Playwright |
| SSR / 全栈 | **Nuxt 3** |
| CSS 方案 | Tailwind CSS / UnoCSS / Scoped CSS |
| 表单校验 | VeeValidate / FormKit / Zod + VueUse |
| 代码规范 | ESLint + Prettier + Vue ESLint Plugin |

---

## 2. 环境搭建与项目创建

### 2.1 前置要求

| 依赖 | 最低版本 | 说明 |
| --- | --- | --- |
| Node.js | 18.x+（推荐 20.x LTS） | `node -v` 验证 |
| 包管理器 | npm 9+ / pnpm 8+ / yarn 4+ | 推荐 **pnpm** |
| 编辑器 | VS Code + Vue - Official 插件 | 提供语法高亮、类型提示、模板补全 |
| Git | 任意近期版本 | 版本管理 |

### 2.2 创建项目（Vite）

```bash
# 使用 npm
npm create vite@latest my-vue-app -- --template vue-ts

# 使用 pnpm（推荐）
pnpm create vite my-vue-app --template vue-ts

# 进入项目并安装依赖
cd my-vue-app
pnpm install

# 启动开发服务器
pnpm dev
```

> `vue-ts` 模板会生成 TypeScript 项目。如果不使用 TypeScript，选择 `vue` 模板。**强烈建议新项目直接使用 TypeScript**。

### 2.3 项目结构

```
my-vue-app/
├── public/                 # 静态资源（不会被 Vite 处理）
│   └── favicon.ico
├── src/
│   ├── assets/             # 静态资源（会被 Vite 处理，如图片、字体）
│   │   └── logo.svg
│   ├── components/         # 可复用组件
│   │   └── HelloWorld.vue
│   ├── composables/        # 组合式函数（可复用逻辑）
│   ├── router/             # 路由配置
│   ├── stores/             # Pinia 状态管理
│   ├── styles/             # 全局样式
│   ├── types/              # TypeScript 类型定义
│   ├── utils/              # 工具函数
│   ├── App.vue             # 根组件
│   └── main.ts             # 入口文件
├── index.html              # HTML 模板
├── vite.config.ts          # Vite 配置
├── tsconfig.json           # TypeScript 配置
├── package.json
└── .gitignore
```

### 2.4 入口文件解析

```typescript
// src/main.ts
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { createPinia } from 'pinia'
import './styles/main.css'

const app = createApp(App)

app.use(createPinia())   // 安装 Pinia
app.use(router)           // 安装 Vue Router

app.mount('#app')         // 挂载到 index.html 中的 #app 节点
```

关键点：

1. `createApp(App)` 创建应用实例（Vue 3 的应用级 API 全部挂在实例上）。
2. `app.use()` 安装插件（路由、状态管理等）。
3. `app.mount('#app')` 将应用挂载到 DOM 节点。

### 2.5 VS Code 推荐配置

安装插件：

- **Vue - Official**（原 Volar）：Vue 语言支持，必装。
- **TypeScript Vue Plugin (Volar)**：Vue 文件中的 TS 支持。
- **ESLint** / **Prettier**：代码规范。
- **Error Lens**：行内错误提示（可选）。

`tsconfig.json` 中启用 Vue 插件：

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "vue"
  }
}
```

### 2.6 安装常用生态

```bash
# 路由
pnpm add vue-router@4

# 状态管理
pnpm add pinia

# 工具库（响应式工具集合，Vue 官方团队维护）
pnpm add @vueuse/core

# HTTP 请求
pnpm add axios

# 开发工具
pnpm add -D eslint eslint-plugin-vue prettier
```

---

## 3. 模板语法与指令

### 3.1 文本插值

```vue
<template>
  <!-- 最常用：双花括号文本插值 -->
  <h1>{{ title }}</h1>
  <p>{{ user.name }} - {{ user.age }}</p>

  <!-- 表达式（不是语句） -->
  <p>{{ count > 10 ? '很多' : '较少' }}</p>
  <p>{{ fullName.toUpperCase() }}</p>
  <p>{{ items.length }} 项</p>
</template>
```

> **注意**：`{{ }}` 中只能放**表达式**（有返回值），不能放语句（`if`、`for` 等）。

### 3.2 v-bind 绑定属性

```vue
<template>
  <!-- 绑定 HTML 属性 -->
  <img :src="imageUrl" :alt="altText">
  <a :href="url" :target="isNewTab ? '_blank' : '_self'">链接</a>

  <!-- 简写：: 就是 v-bind 的语法糖 -->
  <div :class="className" :style="styleObj"></div>

  <!-- 动态属性名（ES6 计算属性名语法） -->
  <div :[dynamicAttr]="'value'"></div>
</template>
```

### 3.3 条件渲染

```vue
<template>
  <!-- v-if / v-else-if / v-else：条件为真时才渲染（会移除/创建 DOM） -->
  <div v-if="status === 'loading'">加载中...</div>
  <div v-else-if="status === 'error'">出错了</div>
  <div v-else>加载完成</div>

  <!-- v-show：始终渲染，通过 CSS display 切换显隐（适合频繁切换） -->
  <div v-show="isVisible">我可能被隐藏</div>

  <!-- v-if 与 v-for 同时使用时的注意事项 -->
  <!-- 错误：v-if 和 v-for 在同一元素上 -->
  <!-- <li v-for="item in items" v-if="item.visible">{{ item.name }}</li> -->

  <!-- 正确做法：用 computed 过滤，或用 <template> 包裹 -->
  <template v-for="item in items" :key="item.id">
    <li v-if="item.visible">{{ item.name }}</li>
  </template>
</template>
```

| 维度 | `v-if` | `v-show` |
| --- | --- | --- |
| 渲染机制 | 条件性地添加/移除 DOM | 始终渲染，切换 CSS `display` |
| 初始开销 | 低（条件为假时不渲染） | 高（始终渲染） |
| 切换开销 | 高（增删 DOM） | 低（仅切 CSS） |
| 适用场景 | 条件很少改变 | 条件频繁改变 |

### 3.4 列表渲染

```vue
<template>
  <!-- 基本用法 -->
  <ul>
    <li v-for="(item, index) in items" :key="item.id">
      {{ index }}. {{ item.name }}
    </li>
  </ul>

  <!-- 遍历对象 -->
  <ul>
    <li v-for="(value, key, index) in userProfile" :key="key">
      {{ index }}. {{ key }}: {{ value }}
    </li>
  </ul>

  <!-- 遍历数字范围 -->
  <ul>
    <li v-for="n in 5" :key="n">{{ n }}</li>
  </ul>

  <!-- 配合 <template> 渲染多个兄弟元素 -->
  <template v-for="group in groups" :key="group.id">
    <h3>{{ group.title }}</h3>
    <p>{{ group.content }}</p>
    <hr>
  </template>
</template>
```

**`key` 的重要性**：

- `key` 是 Vue 虚拟 DOM diff 算法的**节点身份标识**。
- 使用**唯一且稳定的值**（如数据库 `id`），**不要用 `index` 作为 key**（尤其在列表会增删/排序时）。
- 错误的 `key` 会导致组件状态错乱、DOM 复用错误等问题。

### 3.5 事件处理

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)
const message = ref('')

function increment() {
  count.value++
}

function handleClick(event: MouseEvent, extra: string) {
  console.log(event, extra)
}

function onKeyup(event: KeyboardEvent) {
  if (event.key === 'Enter') {
    submit()
  }
}

function submit() {
  console.log('submitted:', message.value)
}
</script>

<template>
  <!-- 基本绑定 -->
  <button @click="increment">{{ count }}</button>

  <!-- 内联表达式 -->
  <button @click="count++">{{ count }}</button>

  <!-- 传参 -->
  <button @click="handleClick($event, 'extra')">带参数</button>

  <!-- 修饰符 -->
  <form @submit.prevent="submit">
    <input v-model="message" @keyup.enter="submit">
  </form>

  <!-- 事件修饰符 -->
  <a @click.stop="onClick">阻止冒泡</a>
  <div @click.capture="onCapture">捕获阶段</div>
  <div @click.self="onSelf">仅自身点击</div>
  <a @click.once="onOnce">仅执行一次</a>
  <div @scroll.passive="onScroll">被动监听</div>

  <!-- 按键修饰符 -->
  <input @keyup.enter="submit">
  <input @keyup.esc="cancel">
  <input @keyup.ctrl.a="selectAll">

  <!-- 系统修饰符 -->
  <div @click.ctrl="onCtrlClick">Ctrl + Click</div>
  <div @click.shift="onShiftClick">Shift + Click</div>
</template>
```

常用修饰符速查：

| 修饰符 | 作用 |
| --- | --- |
| `.stop` | `event.stopPropagation()` |
| `.prevent` | `event.preventDefault()` |
| `.self` | 仅事件目标是元素自身时触发 |
| `.once` | 只触发一次 |
| `.passive` | 声明为被动监听（性能优化） |
| `.capture` | 使用捕获阶段 |
| `.enter` / `.esc` / `.tab` / `.delete` / `.space` / `.up` / `.down` 等 | 按键修饰符 |
| `.ctrl` / `.alt` / `.shift` / `.meta` | 系统修饰符 |

### 3.6 v-model 语法糖

```vue
<template>
  <!-- 等价于 :value + @input -->
  <input v-model="text">
  <textarea v-model="description"></textarea>
  <select v-model="selected">
    <option value="a">A</option>
    <option value="b">B</option>
  </select>

  <!-- 复选框（布尔） -->
  <input type="checkbox" v-model="isChecked">

  <!-- 复选框（数组） -->
  <input type="checkbox" value="vue" v-model="tags">
  <input type="checkbox" value="react" v-model="tags">

  <!-- 单选框 -->
  <input type="radio" value="male" v-model="gender">
  <input type="radio" value="female" v-model="gender">

  <!-- 修饰符 -->
  <input v-model.lazy="text">      <!-- 改为 change 事件触发 -->
  <input v-model.number="age">     <!-- 自动转为 number -->
  <input v-model.trim="name">      <!-- 自动去除首尾空白 -->
</template>
```

### 3.7 v-html 与 v-text

```vue
<template>
  <!-- v-text：设置 textContent（等价于 {{ }}） -->
  <p v-text="message"></p>

  <!-- v-html：设置 innerHTML（有 XSS 风险，仅用于可信内容） -->
  <div v-html="trustedHtml"></div>
</template>
```

> **安全警告**：`v-html` 会渲染原始 HTML，可能导致 XSS 攻击。仅在内容来源可信时使用；永远不要用 `v-html` 渲染用户输入。

### 3.8 其他指令

```vue
<template>
  <!-- v-once：只渲染一次，后续数据变化不再更新 -->
  <p v-once>{{ staticMessage }}</p>

  <!-- v-memo：按条件缓存渲染结果（性能优化） -->
  <div v-memo="[valueA, valueB]">
    <!-- 仅当 valueA 或 valueB 变化时才重新渲染 -->
    <expensive-component :a="valueA" :b="valueB" />
  </div>

  <!-- v-pre：跳过该元素及其子元素的编译 -->
  <div v-pre>{{ 这里不会被编译 }}</div>
</template>
```

---

## 4. 响应式系统

### 4.1 ref — 基本类型的响应式包装

```typescript
import { ref } from 'vue'

// 创建响应式引用
const count = ref(0)
const message = ref('Hello')
const isActive = ref<boolean | null>(null)

// 读取：.value
console.log(count.value)   // 0

// 修改：直接赋 .value
count.value = 1
message.value = 'World'

// 传入对象：内部自动转为 reactive
const user = ref({ name: 'Alice', age: 25 })
user.value.name = 'Bob'    // 响应式生效
```

**为什么需要 `.value`？** JavaScript 的基本类型（number、string、boolean）是按值传递的，无法被直接追踪。`ref` 将值包装在一个响应式对象中，通过 `.value` 访问/修改。

### 4.2 reactive — 对象类型的响应式代理

```typescript
import { reactive } from 'vue'

// 创建响应式对象
const state = reactive({
  count: 0,
  items: [] as string[],
  nested: { deep: { value: 1 } }
})

// 读取/修改：直接访问属性
state.count++
state.items.push('new item')
state.nested.deep.value = 2   // 深层嵌套也响应式
```

**`reactive` 的限制**：

1. **只能接受对象类型**（Object、Array、Map、Set），不能接受基本类型。
2. **不能整体替换**（`state = newObj` 会丢失响应性），只能修改属性。
3. **解构会丢失响应性**：

```typescript
const state = reactive({ count: 0 })
const { count } = state   // count 是普通值，不是响应式的！
count++                   // 不会触发视图更新

// 正确做法：用 toRefs 保持响应性
import { toRefs } from 'vue'
const { count } = toRefs(state)   // count 是 Ref
count.value++
```

### 4.3 ref vs reactive — 如何选择

| 维度 | `ref` | `reactive` |
| --- | --- | --- |
| 基本类型 | ✅ 支持 | ❌ 不支持 |
| 对象类型 | ✅（内部用 `reactive`） | ✅ |
| 模板中使用 | 自动解包（无需 `.value`） | 直接使用 |
| 解构 | ✅ 保持响应性 | ❌ 丢失响应性 |
| 整体替换 | ✅（`ref.value = newObj`） | ❌ |
| TypeScript | 类型友好（`Ref<T>`） | 需要泛型辅助 |
| 推荐场景 | 通用，尤其简单状态 | 复杂的表单/配置对象 |

> **实践建议**：**统一使用 `ref`**。它更简洁、解构安全、TS 类型友好。`reactive` 适合处理大型表单等特定场景。

### 4.4 computed — 计算属性

```typescript
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

// 只读计算属性
const fullName = computed(() => `${firstName.value} ${lastName.value}`)
console.log(fullName.value)  // "John Doe"

// 可写计算属性
const fullNameWritable = computed({
  get: () => `${firstName.value} ${lastName.value}`,
  set: (newVal: string) => {
    const [first, ...rest] = newVal.split(' ')
    firstName.value = first
    lastName.value = rest.join(' ')
  }
})
```

**`computed` vs `methods` 的关键区别**：

| 维度 | `computed` | `methods` |
| --- | --- | --- |
| 缓存 | ✅ 有缓存，依赖不变不重算 | ❌ 每次调用都执行 |
| 触发时机 | 依赖变化时才重新计算 | 每次渲染都调用 |
| 适用 | 从现有状态派生新数据 | 事件处理、副作用 |

```vue
<template>
  <!-- ✅ 好：computed 有缓存，count 不变时不重新计算 -->
  <p>{{ expensiveComputed }}</p>

  <!-- ❌ 差：每次渲染都重新调用 -->
  <p>{{ expensiveMethod() }}</p>
</template>
```

### 4.5 watch — 侦听器

```typescript
import { ref, watch, watchEffect } from 'vue'

const count = ref(0)
const user = reactive({ name: 'Alice', age: 25 })

// 侦听单个 ref
watch(count, (newVal, oldVal) => {
  console.log(`count: ${oldVal} → ${newVal}`)
})

// 侦听多个来源
watch([count, () => user.name], ([newCount, newName], [oldCount, oldName]) => {
  console.log('多个来源变化')
})

// 侦听 reactive 对象（默认深度侦听）
watch(user, (newVal, oldVal) => {
  console.log('user 变化了', newVal)
}, { deep: true })

// 侦听 getter 函数
watch(
  () => user.name,
  (newName, oldName) => {
    console.log(`name: ${oldName} → ${newName}`)
  }
)

// 选项
watch(count, callback, {
  immediate: true,    // 立即执行一次（类似 onMounted）
  deep: true,         // 深度侦听（对象内部变化）
  flush: 'post',      // 在 DOM 更新后触发（'pre' | 'post' | 'sync'）
  once: true          // 只执行一次
})
```

### 4.6 watchEffect — 自动追踪依赖

```typescript
import { ref, watchEffect } from 'vue'

const count = ref(0)
const message = ref('Hello')

// 自动追踪内部使用的响应式数据
watchEffect(() => {
  console.log(`count is ${count.value}, message is ${message.value}`)
  // count 或 message 变化时自动重新执行
})

// 手动停止侦听
const stop = watchEffect(() => {
  console.log(count.value)
})

// 条件停止
setTimeout(() => stop(), 5000)  // 5 秒后停止
```

**`watch` vs `watchEffect` 对比**：

| 维度 | `watch` | `watchEffect` |
| --- | --- | --- |
| 依赖声明 | 显式指定侦听源 | 自动追踪使用的响应式数据 |
| 初始值 | 不执行（除非 `immediate: true`） | 立即执行一次 |
| 新旧值 | ✅ 提供 `newVal` / `oldVal` | ❌ 不提供 |
| 精确控制 | ✅ | 一般 |
| 适用场景 | 需要对比新旧值 / 精确控制 | 副作用自动追踪（如请求、DOM 操作） |

### 4.7 toRef / toRefs — 保持响应性的解构

```typescript
import { reactive, toRef, toRefs } from 'vue'

const state = reactive({ name: 'Alice', age: 25 })

// toRef：将单个属性转为 ref
const nameRef = toRef(state, 'name')
nameRef.value = 'Bob'  // state.name 也会变

// toRefs：将所有属性转为 ref（解构后保持响应性）
const { name, age } = toRefs(state)
name.value = 'Charlie'  // state.name 也变
```

### 4.8 shallowRef / shallowReactive — 浅层响应式

```typescript
import { shallowRef, shallowReactive } from 'vue'

// shallowRef：只有 .value 赋值时才触发更新（内部变化不追踪）
const largeObject = shallowRef({ /* 大量数据 */ })
largeObject.value = { /* 新对象 */ }   // ✅ 触发更新
largeObject.value.key = 'new'          // ❌ 不触发更新

// shallowReactive：只有第一层属性变化才触发更新
const shallowState = shallowReactive({
  nested: { count: 0 }
})
shallowState.nested = { count: 1 }    // ✅ 触发
shallowState.nested.count++            // ❌ 不触发
```

适用场景：存储大型数据（如列表），不需要深度响应式追踪时使用，可减少性能开销。

### 4.9 triggerRef — 手动触发更新

```typescript
import { shallowRef, triggerRef } from 'vue'

const obj = shallowRef({ count: 0 })

// 修改内部属性后手动触发
obj.value.count++
triggerRef(obj)  // 强制触发依赖更新
```

---

## 5. 组件化开发

### 5.1 单文件组件（SFC）结构

```vue
<!-- MyComponent.vue -->
<script setup lang="ts">
// 组合式 API + TypeScript
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <!-- 模板（HTML + Vue 指令） -->
  <div>
    <p>{{ count }}</p>
  </div>
</template>

<style scoped>
/* 样式（scoped 表示只作用于当前组件） */
div {
  color: blue;
}
</style>
```

SFC 的三个区块是可选的，可以只有 `<template>`、只有 `<script>`、或只有 `<style>`。

### 5.2 Props — 父传子

```vue
<!-- Child.vue -->
<script setup lang="ts">
// 方式一：纯类型声明（推荐）
const props = defineProps<{
  title: string
  count?: number          // 可选
  items: string[]
  config: {
    theme: 'light' | 'dark'
    size: number
  }
  callback?: (val: string) => void
}>()

// 方式二：带默认值和运行时校验
const props2 = defineProps({
  title: { type: String, required: true },
  count: { type: Number, default: 0 },
  items: { type: Array as () => string[], default: () => [] },
  status: {
    type: String,
    validator: (v: string) => ['active', 'inactive'].includes(v),
    default: 'active'
  }
})

// 方式三：withDefaults 设置默认值（配合类型声明）
interface Props {
  title: string
  count?: number
  size?: 'sm' | 'md' | 'lg'
}

const props3 = withDefaults(defineProps<Props>(), {
  count: 0,
  size: 'md'
})
</script>

<template>
  <h2>{{ title }}</h2>
  <p>Count: {{ count }}</p>
</template>
```

> **重要**：Props 是**单向数据流**。子组件**永远不应修改 props**。如果需要修改，应该 `$emit` 事件让父组件修改，或者使用 `v-model`。

### 5.3 Emits — 子传父

```vue
<!-- Child.vue -->
<script setup lang="ts">
// 类型声明
const emit = defineEmits<{
  (e: 'update', id: number, value: string): void
  (e: 'delete', id: number): void
  (e: 'close'): void
}>()

// 或简写形式（Vue 3.3+）
const emit2 = defineEmits<{
  update: [id: number, value: string]
  delete: [id: number]
  close: []
}>()

function handleClick() {
  emit('update', 1, 'hello')
}
</script>

<template>
  <button @click="handleClick">Click</button>
  <button @click="emit('close')">Close</button>
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <Child
    :title="'Hello'"
    @update="onUpdate"
    @delete="onDelete"
    @close="onClose"
  />
</template>

<script setup lang="ts">
function onUpdate(id: number, value: string) {
  console.log(id, value)
}
</script>
```

### 5.4 v-model 在组件中的使用

```vue
<!-- CustomInput.vue -->
<script setup lang="ts">
// Vue 3 默认的 v-model 约定：modelValue + update:modelValue
const props = defineProps<{
  modelValue: string
}>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
}>()
</script>

<template>
  <input
    :value="modelValue"
    @input="emit('update:modelValue', ($event.target as HTMLInputElement).value)"
  >
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <!-- 基本用法 -->
  <CustomInput v-model="text" />

  <!-- Vue 3.4+ defineModel 语法糖（更简洁） -->
  <CustomInput2 v-model="text" />
</template>
```

**Vue 3.4+ `defineModel` 语法糖**（强烈推荐）：

```vue
<!-- CustomInput.vue（简化版） -->
<script setup lang="ts">
const model = defineModel<string>()

// 可以直接 v-model="model" 使用
</script>

<template>
  <input v-model="model">
</template>
```

**参数化 `v-model`**（多值绑定）：

```vue
<!-- UserForm.vue -->
<script setup lang="ts">
const name = defineModel<string>('name')
const age = defineModel<number>('age')
</script>

<template>
  <input v-model="name">
  <input v-model="age" type="number">
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <UserForm v-model:name="userName" v-model:age="userAge" />
</template>
```

### 5.5 Slots — 插槽

```vue
<!-- Layout.vue -->
<script setup lang="ts">
</script>

<template>
  <div class="layout">
    <!-- 默认插槽 -->
    <header>
      <slot name="header">默认头部</slot>
    </header>

    <main>
      <!-- 默认插槽（匿名） -->
      <slot>默认内容</slot>
    </main>

    <footer>
      <!-- 作用域插槽：子组件向插槽传递数据 -->
      <slot name="footer" :year="2024" :author="'Vue Team'">
        <p>默认页脚</p>
      </slot>
    </footer>
  </div>
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <Layout>
    <!-- 默认插槽 -->
    <p>页面内容</p>

    <!-- 具名插槽 -->
    <template #header>
      <h1>自定义头部</h1>
    </template>

    <!-- 作用域插槽：接收子组件传递的数据 -->
    <template #footer="{ year, author }">
      <p>© {{ year }} {{ author }}</p>
    </template>
  </Layout>
</template>
```

### 5.6 Teleport — 传送到任意 DOM

```vue
<template>
  <div>
    <button @click="showModal = true">打开弹窗</button>

    <!-- 将弹窗传送到 body 下，避免被父容器的 overflow/transform 裁剪 -->
    <Teleport to="body">
      <div v-if="showModal" class="modal-overlay">
        <div class="modal">
          <h2>弹窗标题</h2>
          <p>弹窗内容</p>
          <button @click="showModal = false">关闭</button>
        </div>
      </div>
    </Teleport>
  </div>
</template>
```

`to` 接受任何 CSS 选择器。常用场景：弹窗（Modal）、通知（Toast）、下拉菜单、全屏遮罩。

### 5.7 Suspense — 异步组件加载

```vue
<!-- App.vue -->
<template>
  <Suspense>
    <!-- 默认内容（异步就绪后显示） -->
    <template #default>
      <AsyncDashboard />
    </template>

    <!-- 加载中的占位内容 -->
    <template #fallback>
      <LoadingSpinner />
    </template>
  </Suspense>
</template>
```

```vue
<!-- AsyncDashboard.vue -->
<script setup lang="ts">
// setup 中的顶层 await 会让组件成为异步组件
const data = await fetch('/api/dashboard').then(r => r.json())
const config = await loadConfig()
</script>

<template>
  <div>{{ data }}</div>
</template>
```

### 5.8 KeepAlive — 缓存组件状态

```vue
<template>
  <!-- 切换路由/组件时保留组件状态（如滚动位置、输入内容） -->
  <KeepAlive :include="['Dashboard', 'Settings']" :max="10">
    <component :is="currentComponent" />
  </KeepAlive>
</template>
```

| 属性 | 说明 |
| --- | --- |
| `include` | 只缓存名称匹配的组件（字符串 / 数组 / 正则） |
| `exclude` | 不缓存名称匹配的组件 |
| `max` | 最大缓存数量，超出后淘汰最久未使用的 |

配合 `onActivated` / `onDeactivated` 生命周期钩子可以感知组件的激活/停用。

---

## 6. 组合式 API 深入

### 6.1 `<script setup>` 语法糖

```vue
<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import MyComponent from './MyComponent.vue'

// 所有在 setup 中定义的变量/函数，模板中可直接使用
const count = ref(0)
const double = computed(() => count.value * 2)

function increment() {
  count.value++
}

// 组件导入后，模板中直接使用 <MyComponent />，无需注册
// defineProps / defineEmits / defineExpose / defineModel 是编译器宏，无需 import
const props = defineProps<{ title: string }>()
const emit = defineEmits<{ (e: 'change', val: number): void }>()

// 对外暴露（默认 setup 返回值/变量不暴露给父组件，需手动 expose）
defineExpose({
  count,
  increment
})
</script>

<template>
  <MyComponent :title="title" @change="onChange" />
</template>
```

`<script setup>` 的核心优势：

1. **更少的样板代码**：无需手动 `return`，无需 `components: {}` 注册。
2. **更好的类型推断**：编译期优化，TS 支持更好。
3. **更好的性能**：编译后的代码更精简。
4. **更直观的代码组织**：所有变量/函数在 setup 作用域内定义即可使用。

### 6.2 组合式函数（Composables）

**组合式函数**是 Vue 3 中复用有状态逻辑的方式（替代 Vue 2 的 Mixins）。

```typescript
// composables/useCounter.ts
import { ref, computed, onUnmounted } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  const double = computed(() => count.value * 2)
  const isPositive = computed(() => count.value > 0)

  function increment(amount = 1) {
    count.value += amount
  }

  function decrement(amount = 1) {
    count.value -= amount
  }

  function reset() {
    count.value = initialValue
  }

  // 自动清理定时器等副作用
  const timer = setInterval(() => {
    if (count.value > 0) count.value--
  }, 1000)

  onUnmounted(() => clearInterval(timer))

  return {
    count,
    double,
    isPositive,
    increment,
    decrement,
    reset
  }
}
```

```vue
<!-- 在组件中使用 -->
<script setup lang="ts">
import { useCounter } from '@/composables/useCounter'

const { count, double, increment, decrement, reset } = useCounter(10)
</script>

<template>
  <p>{{ count }} (double: {{ double }})</p>
  <button @click="increment()">+1</button>
  <button @click="decrement()">-1</button>
  <button @click="reset()">Reset</button>
</template>
```

**命名约定**：以 `use` 开头命名组合式函数。可以返回任意值，但推荐返回普通对象（非 reactive），便于解构。

### 6.3 常用组合式函数示例

```typescript
// composables/useLocalStorage.ts
import { ref, watch } from 'vue'

export function useLocalStorage<T>(key: string, defaultValue: T) {
  const stored = localStorage.getItem(key)
  const data = ref<T>(stored ? JSON.parse(stored) : defaultValue) as Ref<T>

  watch(data, (newVal) => {
    localStorage.setItem(key, JSON.stringify(newVal))
  }, { deep: true })

  return data
}

// composables/useFetch.ts
import { ref, watchEffect, type Ref } from 'vue'

interface UseFetchOptions {
  immediate?: boolean
  headers?: Record<string, string>
}

export function useFetch<T>(url: string | Ref<string>, options: UseFetchOptions = {}) {
  const data = ref<T | null>(null)
  const error = ref<string | null>(null)
  const loading = ref(false)

  const execute = async () => {
    loading.value = true
    error.value = null
    try {
      const response = await fetch(typeof url === 'string' ? url : url.value, {
        headers: options.headers
      })
      if (!response.ok) throw new Error(`HTTP ${response.status}`)
      data.value = await response.json()
    } catch (e) {
      error.value = (e as Error).message
    } finally {
      loading.value = false
    }
  }

  if (options.immediate !== false) {
    watchEffect(() => {
      // url 变化时重新请求
      if (typeof url !== 'string') {
        watchEffect(execute)
      } else {
        execute()
      }
    })
  }

  return { data, error, loading, execute }
}

// composables/useDebounce.ts
import { ref, watch, type Ref } from 'vue'

export function useDebounce<T>(source: Ref<T>, delay = 300): Ref<T> {
  const debounced = ref(source.value) as Ref<T>

  watch(source, (newVal) => {
    const timer = setTimeout(() => {
      debounced.value = newVal
    }, delay)

    return () => clearTimeout(timer)
  })

  return debounced
}
```

### 6.4 provide / inject — 跨层级通信

```vue
<!-- 顶层祖先组件 -->
<script setup lang="ts">
import { provide, ref, readonly } from 'vue'

const theme = ref('dark')
const config = ref({ apiUrl: 'https://api.example.com' })

// provide(key, value)
provide('theme', theme)
provide('config', readonly(config))   // 用 readonly 防止子组件修改

// 提供方法供子组件调用
provide('toggleTheme', () => {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
})
</script>
```

```vue
<!-- 任意深度的后代组件 -->
<script setup lang="ts">
import { inject, type Ref } from 'vue'

const theme = inject<Ref<string>>('theme')
const config = inject<{ apiUrl: string }>('config')
const toggleTheme = inject<() => void>('toggleTheme')

// 可设置默认值
const fallback = inject('nonExistent', 'default-value')
</script>

<template>
  <p>当前主题：{{ theme }}</p>
  <button @click="toggleTheme">切换主题</button>
</template>
```

**使用建议**：

- `provide/inject` 适合**跨层级传递**（如主题、语言、全局配置），不适合替代 props 用于父子通信。
- 对于可能被修改的数据，用 `readonly()` 包装后再 provide，避免数据流向混乱。
- 可将 `InjectionKey`（Symbol）作为 key 提供更好的 TypeScript 类型安全。

### 6.5 defineAsyncComponent — 异步组件

```vue
<script setup lang="ts">
import { defineAsyncComponent, shallowRef } from 'vue'

// 基本用法
const HeavyChart = defineAsyncComponent(() =>
  import('./components/HeavyChart.vue')
)

// 带加载/错误状态
const HeavyChart2 = defineAsyncComponent({
  loader: () => import('./components/HeavyChart.vue'),
  loadingComponent: LoadingSpinner,      // 加载中显示
  errorComponent: ErrorDisplay,           // 加载失败显示
  delay: 200,                             // 延迟显示 loading（ms）
  timeout: 10000,                         // 超时时间（ms）
  onError(error, retry, fail, attempts) {
    if (attempts < 3) {
      retry()    // 重试
    } else {
      fail()     // 放弃
    }
  }
})
</script>

<template>
  <HeavyChart />
</template>
```

### 6.6 effectScope — 作用域管理

```typescript
import { effectScope, ref, watch } from 'vue'

const scope = effectScope()

scope.run(() => {
  const doubled = ref(0)
  watch(doubled, () => {
    console.log('doubled changed')
  })

  // 这里创建的响应式效果会在 scope 停止时自动清理
})

// 手动停止作用域，清理所有内部效果
scope.stop()
```

用于在非组件上下文中管理响应式副作用的生命周期（如组合式函数中的工具类封装）。

---

## 7. 生命周期

### 7.1 组合式 API 中的生命周期钩子

```vue
<script setup lang="ts">
import {
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
  onActivated,
  onDeactivated,
  onRenderTracked,
  onRenderTriggered,
  onErrorCaptured
} from 'vue'

// setup() 执行时机（等同于 Vue 2 的 beforeCreate + created）
// 以下按执行顺序排列

onBeforeMount(() => {
  // DOM 挂载前调用，此时模板已编译但未渲染
  console.log('beforeMount')
})

onMounted(() => {
  // DOM 挂载完成后调用（可访问真实 DOM）
  console.log('mounted')
  // 典型用途：发起请求、初始化第三方库、操作 DOM
})

onBeforeUpdate(() => {
  // 响应式数据变化导致 DOM 更新前
  console.log('beforeUpdate')
})

onUpdated(() => {
  // DOM 更新完成后
  console.log('updated')
  // 注意：在此修改数据可能导致无限循环
})

onBeforeUnmount(() => {
  // 组件卸载前
  console.log('beforeUnmount')
})

onUnmounted(() => {
  // 组件卸载后（清理定时器、取消订阅等）
  console.log('unmounted')
})

// KeepAlive 专属
onActivated(() => {
  // 被 KeepAlive 缓存的组件激活时
})

onDeactivated(() => {
  // 被 KeepAlive 缓存的组件停用时
})

// 调试
onRenderTracked((event) => {
  console.log('render tracked:', event)
})

onRenderTriggered((event) => {
  console.log('render triggered:', event)
})

// 错误捕获
onErrorCaptured((err, instance, info) => {
  console.error('Error captured:', err)
  return false  // 阻止错误向上传播
})
</script>
```

### 7.2 执行顺序图

```
setup()                        ← beforeCreate + created
  ↓
onBeforeMount                  ← DOM 未挂载
  ↓
  [DOM 挂载]
  ↓
onMounted                      ← DOM 已挂载，可操作 DOM

  --- 响应式数据变化 ---

onBeforeUpdate                 ← DOM 更新前
  ↓
  [DOM 更新]
  ↓
onUpdated                      ← DOM 更新后

  --- 组件卸载 ---

onBeforeUnmount                ← 卸载前
  ↓
  [卸载]
  ↓
onUnmounted                    ← 卸载后
```

### 7.3 Options API 中的生命周期（对比参考）

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  // Options API 生命周期
  beforeCreate() {},   // 等同于 setup() 的开头
  created() {},        // 等同于 setup() 的结尾

  beforeMount() {},    // 等同于 onBeforeMount
  mounted() {},        // 等同于 onMounted

  beforeUpdate() {},   // 等同于 onBeforeUpdate
  updated() {},        // 等同于 onUpdated

  beforeUnmount() {},  // 等同于 onBeforeUnmount（Vue 2 为 beforeDestroy）
  unmounted() {},      // 等同于 onUnmounted（Vue 2 为 destroyed）
})
</script>
```

---

## 8. 表单处理与双向绑定

### 8.1 基础表单绑定

```vue
<script setup lang="ts">
import { ref, reactive } from 'vue'

// 基础表单状态
const form = reactive({
  username: '',
  email: '',
  password: '',
  age: 18,
  gender: 'male',
  hobbies: [] as string[],
  agree: false,
  country: 'china',
  bio: ''
})

// 提交处理
function handleSubmit() {
  console.log('Form data:', form)
  // 发送请求...
}
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <!-- 文本输入 -->
    <input v-model="form.username" placeholder="用户名">
    <input v-model="form.email" type="email" placeholder="邮箱">
    <input v-model="form.password" type="password" placeholder="密码">
    <input v-model.number="form.age" type="number" placeholder="年龄">

    <!-- 文本域 -->
    <textarea v-model="form.bio" placeholder="个人简介"></textarea>

    <!-- 单选框 -->
    <label>
      <input type="radio" value="male" v-model="form.gender"> 男
    </label>
    <label>
      <input type="radio" value="female" v-model="form.gender"> 女
    </label>

    <!-- 复选框（布尔） -->
    <label>
      <input type="checkbox" v-model="form.agree"> 同意条款
    </label>

    <!-- 复选框（数组） -->
    <label>
      <input type="checkbox" value="reading" v-model="form.hobbies"> 阅读
    </label>
    <label>
      <input type="checkbox" value="coding" v-model="form.hobbies"> 编程
    </label>

    <!-- 下拉选择 -->
    <select v-model="form.country">
      <option value="china">中国</option>
      <option value="usa">美国</option>
      <option value="japan">日本</option>
    </select>

    <button type="submit">提交</button>
  </form>
</template>
```

### 8.2 v-model 修饰符

```vue
<template>
  <!-- .lazy：改为 change 事件触发（失焦或回车时） -->
  <input v-model.lazy="text">

  <!-- .number：自动将输入值转为 number -->
  <input v-model.number="age">

  <!-- .trim：自动去除首尾空白 -->
  <input v-model.trim="name">
</template>
```

### 8.3 自定义组件的 v-model

```vue
<!-- CustomSelect.vue -->
<script setup lang="ts">
interface Option {
  label: string
  value: string | number
}

const props = defineProps<{
  options: Option[]
  placeholder?: string
}>()

const model = defineModel<string | number>()

function handleChange(event: Event) {
  model.value = (event.target as HTMLSelectElement).value
}
</script>

<template>
  <select :value="model" @change="handleChange">
    <option value="" disabled>{{ placeholder || '请选择' }}</option>
    <option v-for="opt in options" :key="opt.value" :value="opt.value">
      {{ opt.label }}
    </option>
  </select>
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <CustomSelect
    v-model="selectedCity"
    :options="cityOptions"
    placeholder="选择城市"
  />
</template>
```

### 8.4 表单校验

**方式一：手动校验**

```typescript
import { ref, reactive } from 'vue'

const form = reactive({ email: '', password: '' })
const errors = reactive<{ email?: string; password?: string }>({})

const validators = {
  email: (val: string) => {
    if (!val) return '邮箱不能为空'
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(val)) return '邮箱格式不正确'
    return ''
  },
  password: (val: string) => {
    if (!val) return '密码不能为空'
    if (val.length < 8) return '密码至少 8 位'
    return ''
  }
}

function validateField(field: 'email' | 'password') {
  errors[field] = validators[field](form[field])
}

function validateAll(): boolean {
  let valid = true
  for (const field of Object.keys(validators) as Array<keyof typeof validators>) {
    const error = validators[field](form[field])
    if (error) {
      errors[field] = error
      valid = false
    } else {
      delete errors[field]
    }
  }
  return valid
}

function handleSubmit() {
  if (validateAll()) {
    // 提交
  }
}
```

**方式二：使用校验库（如 Zod + 自定义 composable）**

```typescript
// composables/useFormValidation.ts
import { reactive, computed } from 'vue'
import { z, type ZodSchema } from 'zod'

export function useFormValidation<T>(schema: ZodSchema<T>, initialValues: T) {
  const form = reactive({ ...initialValues }) as T
  const errors = reactive<Record<string, string>>({})
  const touched = reactive<Record<string, boolean>>({})

  const isValid = computed(() => {
    return schema.safeParse(form).success
  })

  function validate(): boolean {
    const result = schema.safeParse(form)
    // 清空旧错误
    for (const key of Object.keys(errors)) delete errors[key]

    if (!result.success) {
      for (const issue of result.error.issues) {
        errors[issue.path[0] as string] = issue.message
      }
      return false
    }
    return true
  }

  return { form, errors, touched, isValid, validate }
}
```

```typescript
// 使用
const schema = z.object({
  email: z.string().email('邮箱格式不正确'),
  password: z.string().min(8, '密码至少 8 位'),
  age: z.number().min(18, '必须年满 18 岁')
})

const { form, errors, validate } = useFormValidation(schema, {
  email: '',
  password: '',
  age: 18
})
```

---

## 9. 过渡与动画

### 9.1 Transition 组件

```vue
<script setup lang="ts">
import { ref } from 'vue'

const show = ref(true)
const activeTab = ref('home')
</script>

<template>
  <button @click="show = !show">切换</button>

  <!-- 自动应用过渡 CSS -->
  <Transition name="fade">
    <div v-if="show">Hello, Transition!</div>
  </Transition>

  <!-- 列表过渡 -->
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">{{ item.name }}</li>
  </TransitionGroup>
</template>

<style>
/* fade 过渡 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* list 过渡 */
.list-enter-active,
.list-leave-active {
  transition: all 0.4s ease;
}

.list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* 移动动画（列表重排序时） */
.list-move {
  transition: transform 0.4s ease;
}

/* 离开的元素从文档流中移除 */
.list-leave-active {
  position: absolute;
}
</style>
```

### 9.2 Transition 钩子

```vue
<script setup lang="ts">
import { ref } from 'vue'

const show = ref(true)

function onBeforeEnter(el: Element) {
  console.log('before enter', el)
}

function onEnter(el: Element, done: () => void) {
  // 使用 JS 动画
  const element = el as HTMLElement
  element.style.opacity = '0'
  requestAnimationFrame(() => {
    element.style.transition = 'opacity 0.5s'
    element.style.opacity = '1'
    done()
  })
}

function onLeave(el: Element, done: () => void) {
  const element = el as HTMLElement
  element.style.opacity = '0'
  setTimeout(done, 500)
}
</script>

<template>
  <Transition
    :css="false"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
    @leave="onLeave"
  >
    <div v-if="show">JS 动画</div>
  </Transition>
</template>
```

### 9.3 Transition 常用属性

```vue
<Transition
  name="fade"
  mode="out-in"        <!-- 先离开再进入（避免重叠） -->
  :duration="300"       <!-- 自定义持续时间（ms） -->
  :appear="true"        <!-- 初始渲染时也应用过渡 -->
>
  <component :is="currentView" />
</Transition>
```

| `mode` | 行为 |
| --- | --- |
| `in-out` | 新元素先进入，旧元素后离开 |
| `out-in` | 旧元素先离开，新元素后进入（推荐） |
| 默认 | 同时进行 |

### 9.4 使用动画库

配合 CSS 动画库（如 [Animate.css](https://animate.style)）：

```vue
<Transition
  enter-active-class="animate__animated animate__fadeIn"
  leave-active-class="animate__animated animate__fadeOut"
>
  <div v-if="show">使用 Animate.css</div>
</Transition>
```

---

## 继续请看第二部分！
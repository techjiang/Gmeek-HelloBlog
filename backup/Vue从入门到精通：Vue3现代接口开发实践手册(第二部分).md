## 10. 自定义指令

### 10.1 基础自定义指令

```typescript
// directives/vFocus.ts
import type { Directive, DirectiveBinding } from 'vue'

export const vFocus: Directive<HTMLElement, boolean> = {
  // 元素挂载后触发
  mounted(el, binding) {
    if (binding.value !== false) {
      el.focus()
    }
  },

  // 元素更新前
  beforeUpdate(el, binding) {
    console.log('before update')
  },

  // 元素更新后
  updated(el, binding) {
    console.log('updated')
  },

  // 元素卸载前
  beforeUnmount(el) {
    console.log('before unmount')
  },

  // 元素卸载后
  unmounted(el) {
    console.log('unmounted')
  }
}
```

### 10.2 使用自定义指令

```vue
<script setup lang="ts">
import { vFocus } from '@/directives/vFocus'
import { vPermission } from '@/directives/vPermission'
</script>

<template>
  <!-- 自动聚焦 -->
  <input v-focus>

  <!-- 条件聚焦 -->
  <input v-focus="shouldFocus">

  <!-- 权限指令 -->
  <button v-permission="'admin'">仅管理员可见</button>
  <button v-permission="['admin', 'editor']">管理员或编辑可见</button>
</template>
```

### 10.3 实用指令示例

```typescript
// directives/vPermission.ts
import type { Directive } from 'vue'
import { useAuthStore } from '@/stores/auth'

export const vPermission: Directive<HTMLElement, string | string[]> = {
  mounted(el, binding) {
    const auth = useAuthStore()
    const required = Array.isArray(binding.value) ? binding.value : [binding.value]
    const hasPermission = required.some((perm) => auth.permissions.includes(perm))

    if (!hasPermission) {
      el.parentNode?.removeChild(el)   // 移除 DOM
      // 或者：el.style.display = 'none'
    }
  }
}

// directives/vDebounce.ts
import type { Directive } from 'vue'

export const vDebounce: Directive<HTMLElement, () => void> = {
  mounted(el, binding) {
    let timer: ReturnType<typeof setTimeout>
    el.addEventListener('click', () => {
      clearTimeout(timer)
      timer = setTimeout(() => {
        binding.value()
      }, binding.arg ? parseInt(binding.arg) : 300)
    })
  }
}

// directives/vClickOutside.ts
import type { Directive } from 'vue'

export const vClickOutside: Directive<HTMLElement, (e: MouseEvent) => void> = {
  mounted(el, binding) {
    el.__clickOutsideHandler = (event: MouseEvent) => {
      if (!el.contains(event.target as Node)) {
        binding.value(event)
      }
    }
    document.addEventListener('click', el.__clickOutsideHandler)
  },
  unmounted(el) {
    document.removeEventListener('click', el.__clickOutsideHandler)
  }
}
```

---

## 11. 插件与全局配置

### 11.1 创建插件

```typescript
// plugins/analytics.ts
import type { App, Plugin } from 'vue'

interface AnalyticsOptions {
  trackingId: string
  debug?: boolean
}

export const AnalyticsPlugin: Plugin = {
  install(app: App, options: AnalyticsOptions) {
    // 全局属性
    app.config.globalProperties.$analytics = {
      track(event: string, data?: Record<string, unknown>) {
        if (options.debug) {
          console.log(`[Analytics] ${event}`, data)
        }
        // 发送到分析服务
      }
    }

    // 全局指令
    app.directive('track', {
      mounted(el, binding) {
        el.addEventListener('click', () => {
          app.config.globalProperties.$analytics.track(binding.value)
        })
      }
    })

    // 全局组件
    app.component('AnalyticsBanner', {
      template: '<div class="analytics-banner">Cookie Consent</div>'
    })

    // provide 全局数据
    app.provide('analyticsConfig', options)
  }
}
```

### 11.2 使用插件

```typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import { AnalyticsPlugin } from './plugins/analytics'

const app = createApp(App)

app.use(AnalyticsPlugin, {
  trackingId: 'UA-XXXXX',
  debug: import.meta.env.DEV
})

app.mount('#app')
```

```vue
<!-- 在组件中使用 -->
<script setup lang="ts">
import { inject } from 'vue'
import { getCurrentInstance } from 'vue'

// 使用全局属性
const { proxy } = getCurrentInstance()!
proxy?.$analytics.track('page_view', { page: '/home' })

// 使用注入
const config = inject<{ trackingId: string }>('analyticsConfig')
</script>

<template>
  <!-- 使用全局指令 -->
  <button v-track="'button_click'">跟踪点击</button>

  <!-- 使用全局组件 -->
  <AnalyticsBanner />
</template>
```

### 11.3 全局配置

```typescript
// main.ts
const app = createApp(App)

// 全局错误处理
app.config.errorHandler = (err, instance, info) => {
  console.error('Global error:', err, info)
  // 发送到错误监控服务
}

// 全局警告处理（开发模式）
app.config.warnHandler = (msg, instance, trace) => {
  console.warn('Vue warning:', msg)
}

// 全局属性（不推荐过度使用，优先使用 provide/inject 或 Pinia）
app.config.globalProperties.$formatDate = (date: Date) => {
  return date.toLocaleDateString('zh-CN')
}

// 自定义元素（将 Vue 组件用作 Web Component）
app.config.compilerOptions.isCustomElement = (tag) => {
  return tag.startsWith('my-')
}
```

---

## 12. Vue Router 路由

### 12.1 基础配置

```typescript
// router/index.ts
import { createRouter, createWebHistory, type RouteRecordRaw } from 'vue-router'

const routes: RouteRecordRaw[] = [
  {
    path: '/',
    name: 'Home',
    component: () => import('@/views/Home.vue')
  },
  {
    path: '/about',
    name: 'About',
    component: () => import('@/views/About.vue')
  },
  {
    path: '/users/:id',
    name: 'UserDetail',
    component: () => import('@/views/UserDetail.vue'),
    props: true   // 将路由参数作为 props 传递给组件
  },
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: () => import('@/views/Dashboard.vue'),
    meta: { requiresAuth: true },
    children: [
      {
        path: '',
        name: 'DashboardHome',
        component: () => import('@/views/dashboard/Home.vue')
      },
      {
        path: 'settings',
        name: 'Settings',
        component: () => import('@/views/dashboard/Settings.vue')
      }
    ]
  },
  {
    path: '/:pathMatch(.*)*',
    name: 'NotFound',
    component: () => import('@/views/NotFound.vue')
  }
]

const router = createRouter({
  history: createWebHistory(),     // HTML5 History 模式
  routes,
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) return savedPosition
    return { top: 0 }
  }
})

export default router
```

### 12.2 在组件中使用路由

```vue
<script setup lang="ts">
import { useRouter, useRoute } from 'vue-router'
import { computed } from 'vue'

const router = useRouter()
const route = useRoute()

// 读取路由参数
const userId = computed(() => route.params.id)
const query = computed(() => route.query)

// 导航
function goToUser(id: number) {
  router.push({ name: 'UserDetail', params: { id } })
}

function goToSearch() {
  router.push({ path: '/search', query: { q: 'vue', page: '1' } })
}

function goBack() {
  router.back()
}

function replace() {
  // 不会在历史记录中留下痕迹
  router.replace({ name: 'Home' })
}
</script>

<template>
  <!-- 声明式导航 -->
  <RouterLink to="/">首页</RouterLink>
  <RouterLink :to="{ name: 'UserDetail', params: { id: 42 } }">用户 42</RouterLink>
  <RouterLink to="/dashboard" active-class="active">仪表盘</RouterLink>

  <!-- 精确匹配激活类 -->
  <RouterLink to="/dashboard" exact-active-class="exact-active">仪表盘</RouterLink>

  <!-- 编程式导航 -->
  <button @click="goToUser(42)">去用户 42</button>
  <button @click="goBack">返回</button>

  <!-- 渲染匹配的组件 -->
  <RouterView />
</template>
```

### 12.3 导航守卫

```typescript
// router/index.ts
router.beforeEach((to, from, next) => {
  // 全局前置守卫
  const authStore = useAuthStore()

  if (to.meta.requiresAuth && !authStore.isLoggedIn) {
    // 未登录，跳转到登录页
    next({ name: 'Login', query: { redirect: to.fullPath } })
  } else if (to.name === 'Login' && authStore.isLoggedIn) {
    // 已登录，跳转到首页
    next({ name: 'Home' })
  } else {
    next()
  }
})

router.afterEach((to, from) => {
  // 全局后置钩子（适合设置页面标题、埋点等）
  document.title = to.meta.title ? `${to.meta.title} - MyApp` : 'MyApp'
})

// 路由独享守卫
{
  path: '/admin',
  component: AdminLayout,
  beforeEnter: (to, from, next) => {
    if (!isAdmin()) {
      next('/403')
    } else {
      next()
    }
  }
}
```

```vue
<!-- 组件内守卫 -->
<script setup lang="ts">
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'

// 路由离开前（如表单未保存提醒）
onBeforeRouteLeave((to, from) => {
  if (hasUnsavedChanges.value) {
    return window.confirm('有未保存的更改，确定离开？')
  }
})

// 路由参数变化时（同一组件复用时）
onBeforeRouteUpdate(async (to, from) => {
  if (to.params.id !== from.params.id) {
    await fetchUser(to.params.id as string)
  }
})
</script>
```

### 12.4 路由懒加载

```typescript
// ✅ 推荐：动态 import 实现懒加载
const routes = [
  {
    path: '/dashboard',
    component: () => import('@/views/Dashboard.vue')
  },
  {
    path: '/admin',
    component: () => import(/* webpackChunkName: "admin" */ '@/views/Admin.vue')
  }
]

// ✅ 配合 Suspense + defineAsyncComponent
import { defineAsyncComponent } from 'vue'
const HeavyView = defineAsyncComponent(() => import('@/views/HeavyView.vue'))
```

### 12.5 路由元信息与类型

```typescript
// 扩展 RouteMeta 类型
declare module 'vue-router' {
  interface RouteMeta {
    title?: string
    requiresAuth?: boolean
    roles?: string[]
    layout?: 'default' | 'admin' | 'blank'
  }
}
```

---

## 13. Pinia 状态管理

### 13.1 为什么用 Pinia

Pinia 是 Vue 官方推荐的状态管理库（替代 Vuex）：

- **更简单的 API**：没有 mutations，直接在 actions 中修改状态。
- **完整的 TypeScript 支持**：类型推断极佳。
- **轻量**：约 1KB（gzip 后）。
- **模块化**：每个 store 独立，无需嵌套模块。
- **支持组合式 API 风格**：与 `<script setup>` 天然契合。
- **支持 SSR / 热更新 / Devtools**。

### 13.2 定义 Store

**选项式风格（Options Store）**：

```typescript
// stores/counter.ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
    name: 'Counter'
  }),

  getters: {
    double: (state) => state.count * 2,
    // 引用其他 getter
    doublePlusOne(): number {
      return this.double + 1
    }
  },

  actions: {
    increment(amount = 1) {
      this.count += amount
    },

    async fetchCount() {
      const response = await fetch('/api/count')
      this.count = await response.json()
    }
  }
})
```

**组合式风格（Setup Store，更推荐）**：

```typescript
// stores/counter.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCounterStore = defineStore('counter', () => {
  // state → ref
  const count = ref(0)
  const name = ref('Counter')

  // getters → computed
  const double = computed(() => count.value * 2)
  const doublePlusOne = computed(() => double.value + 1)

  // actions → functions
  function increment(amount = 1) {
    count.value += amount
  }

  async function fetchCount() {
    const response = await fetch('/api/count')
    count.value = await response.json()
  }

  return { count, name, double, doublePlusOne, increment, fetchCount }
})
```

### 13.3 在组件中使用

```vue
<script setup lang="ts">
import { storeToRefs } from 'pinia'
import { useCounterStore } from '@/stores/counter'

const counterStore = useCounterStore()

// ✅ 使用 storeToRefs 保持响应性（解构 state 和 getters）
const { count, double, doublePlusOne } = storeToRefs(counterStore)

// ✅ actions 直接解构即可（不需要响应性）
const { increment, fetchCount } = counterStore

// ❌ 错误：直接解构 state 会丢失响应性
// const { count } = counterStore  // count 是普通值！

function handleClick() {
  increment(5)
}
</script>

<template>
  <p>Count: {{ count }}</p>
  <p>Double: {{ double }}</p>
  <button @click="handleClick">+5</button>
  <button @click="fetchCount">Fetch</button>
</template>
```

### 13.4 Store 之间的组合

```typescript
// stores/cart.ts
import { defineStore } from 'pinia'
import { useUserStore } from './user'

export const useCartStore = defineStore('cart', () => {
  const userStore = useUserStore()  // 在 setup store 中可以直接引用其他 store

  const items = ref<CartItem[]>([])
  const total = computed(() => items.value.reduce((sum, item) => sum + item.price, 0))

  function addItem(item: CartItem) {
    if (!userStore.isLoggedIn) {
      throw new Error('请先登录')
    }
    items.value.push(item)
  }

  return { items, total, addItem }
})
```

### 13.5 持久化存储

```bash
pnpm add pinia-plugin-persistedstate
```

```typescript
// main.ts
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)
app.use(pinia)
```

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const token = ref('')
  const user = ref<User | null>(null)

  return { token, user }
}, {
  persist: {
    key: 'user-store',
    storage: localStorage,   // 或 sessionStorage
    pick: ['token']          // 仅持久化指定字段
  }
})
```

### 13.6 Devtools 集成

Pinia 完美集成 Vue Devtools：

- 查看所有 store 的状态。
- 实时编辑 state。
- 追踪 action 调用。
- 时间旅行调试。

---

## 14. TypeScript 集成

### 14.1 基础类型标注

```vue
<script setup lang="ts">
import { ref, reactive, computed } from 'vue'

// ref 显式标注
const count = ref<number>(0)
const message = ref<string>('Hello')
const user = ref<User | null>(null)

// reactive 标注
interface FormState {
  name: string
  email: string
  age: number
  tags: string[]
}

const form = reactive<FormState>({
  name: '',
  email: '',
  age: 18,
  tags: []
})

// computed 标注
const isValid = computed<boolean>(() => form.name.length > 0)

// 函数标注
function greet(name: string): string {
  return `Hello, ${name}!`
}
</script>
```

### 14.2 组件 Props 类型

```vue
<script setup lang="ts">
// 接口定义
interface Props {
  title: string
  items: User[]
  config?: {
    theme: 'light' | 'dark'
    size: 'sm' | 'md' | 'lg'
  }
  onUpdate?: (id: number, data: Partial<User>) => void
}

// withDefaults 设置默认值
const props = withDefaults(defineProps<Props>(), {
  config: () => ({ theme: 'light', size: 'md' }),
  onUpdate: undefined
})

// Emits 类型
const emit = defineEmits<{
  (e: 'update', id: number, data: Partial<User>): void
  (e: 'delete', id: number): void
  (e: 'close'): void
}>()
</script>
```

### 14.3 模板引用类型

```vue
<script setup lang="ts">
import { ref, onMounted, type ComponentPublicInstance } from 'vue'

// DOM 元素引用
const inputRef = ref<HTMLInputElement | null>(null)
const divRef = ref<HTMLDivElement | null>(null)

// 组件引用
const childRef = ref<InstanceType<typeof ChildComponent> | null>(null)

onMounted(() => {
  inputRef.value?.focus()

  // 调用子组件暴露的方法
  childRef.value?.validate()
})
</script>

<template>
  <input ref="inputRef">
  <div ref="divRef"></div>
  <ChildComponent ref="childRef" />
</template>
```

### 14.4 类型工具

```typescript
import type {
  // Props / Emits 类型
  PropType,
  // 组件实例
  ComponentPublicInstance,
  // Setup 上下文
  SetupContext,
  // VNode
  VNode,
  // 指令
  Directive,
  // 插件
  Plugin
} from 'vue'

// 推断 defineProps 的类型
type Props = {
  [K in keyof InstanceType<typeof MyComponent>['$props']]: InstanceType<typeof MyComponent>['$props'][K]
}

// 获取 emits 的参数类型
type EmitParams = Parameters<InstanceType<typeof MyComponent>['$emit']>
```

### 14.5 VueUse — 实用工具集合

```bash
pnpm add @vueuse/core
```

```vue
<script setup lang="ts">
import {
  useMouse,          // 鼠标位置
  useWindowSize,     // 窗口尺寸
  useLocalStorage,   // localStorage 响应式
  useDark,           // 深色模式
  useEventListener,  // 事件监听
  useFetch,          // 请求
  useIntersectionObserver,  // 可见性检测
  useClipboard,      // 剪贴板
  useDebounceFn,     // 防抖
  useThrottleFn      // 节流
} from '@vueuse/core'

const { x, y } = useMouse()
const { width, height } = useWindowSize()
const isDark = useDark()
const { copy, copied } = useClipboard()
const { data, error, isFetching } = useFetch('/api/data')

const search = ref('')
const debouncedSearch = useDebounceFn((query: string) => {
  console.log('搜索:', query)
}, 300)
</script>
```

---

## 15. 性能优化

### 15.1 编译期优化

| 特性 | 说明 |
| --- | --- |
| `v-once` | 静态内容只渲染一次 |
| `v-memo` | 按条件缓存渲染结果 |
| `v-show` vs `v-if` | 频繁切换用 `v-show`，条件渲染用 `v-if` |
| `shallowRef` / `shallowReactive` | 大数据减少深度追踪 |
| `reactive` 对象优化 | 避免深层嵌套对象的不必要响应式 |

### 15.2 组件级优化

```vue
<script setup lang="ts">
import { defineAsyncComponent, shallowRef, markRaw } from 'vue'

// 1. 异步组件（代码分割）
const HeavyChart = defineAsyncComponent(() => import('./HeavyChart.vue'))

// 2. markRaw：标记对象为非响应式（避免大型对象的 Proxy 开销）
const chartInstance = markRaw(new ChartLibrary())

// 3. v-memo：缓存列表项渲染
const items = ref(Array.from({ length: 1000 }, (_, i) => ({ id: i, name: `Item ${i}` })))

// 4. 合理使用 computed 缓存
const filteredItems = computed(() => {
  return items.value.filter(item => item.name.includes(search.value))
})

// 5. 事件处理优化（避免内联函数创建）
// ❌ 差：<div @click="() => doSomething(item)">
// ✅ 好：<div @click="handleClick(item)">
</script>

<template>
  <!-- 6. v-memo 缓存列表渲染 -->
  <div v-for="item in filteredItems" :key="item.id" v-memo="[item.name, selectedId === item.id]">
    {{ item.name }}
  </div>
</template>
```

### 15.3 虚拟滚动

对于长列表（1000+ 项），使用虚拟滚动库：

```bash
pnpm add @tanstack/vue-virtual
```

```vue
<script setup lang="ts">
import { useVirtualizer } from '@tanstack/vue-virtual'
import { ref } from 'vue'

const parentRef = ref<HTMLElement | null>(null)
const items = ref(Array.from({ length: 10000 }, (_, i) => ({ id: i, name: `Item ${i}` })))

const virtualizer = useVirtualizer({
  count: items.value.length,
  getScrollElement: () => parentRef.value,
  estimateSize: () => 50,
  overscan: 5
})
</script>

<template>
  <div ref="parentRef" style="height: 400px; overflow: auto;">
    <div :style="{ height: `${virtualizer.getTotalSize()}px`, position: 'relative' }">
      <div
        v-for="virtualRow in virtualizer.getVirtualItems()"
        :key="virtualRow.key"
        :style="{
          position: 'absolute',
          top: 0,
          left: 0,
          width: '100%',
          height: `${virtualRow.size}px`,
          transform: `translateY(${virtualRow.start}px)`
        }"
      >
        {{ items[virtualRow.index].name }}
      </div>
    </div>
  </div>
</template>
```

### 15.4 构建优化

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    vue(),
    visualizer({ open: true })   // 分析 bundle 体积
  ],

  build: {
    // 手动分包
    rollupOptions: {
      output: {
        manualChunks: {
          vue: ['vue', 'vue-router', 'pinia'],
          ui: ['element-plus'],
          utils: ['lodash-es', 'dayjs']
        }
      }
    },

    // 压缩
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,    // 移除 console
        drop_debugger: true    // 移除 debugger
      }
    },

    // 产物大小警告
    chunkSizeWarningLimit: 500
  }
})
```

### 15.5 运行时性能清单

- [ ] 使用 `v-once` 渲染静态内容
- [ ] 长列表使用虚拟滚动
- [ ] 大对象用 `markRaw` 或 `shallowRef`
- [ ] 避免深层嵌套的响应式数据
- [ ] 事件处理函数不使用内联箭头函数
- [ ] 合理使用 `computed` 缓存（vs `methods`）
- [ ] 使用 `defineAsyncComponent` 做代码分割
- [ ] 优化图片（WebP、懒加载）
- [ ] 路由懒加载
- [ ] 开发环境避免使用 `watch` 的 `deep: true`（改用 `watchEffect` 或精确路径）

---

## 16. 测试

### 16.1 测试工具链

| 工具 | 用途 |
| --- | --- |
| **Vitest** | 单元测试 / 集成测试（Vite 原生，速度快） |
| **Vue Test Utils** | Vue 组件测试工具库 |
| **Playwright** | E2E 端到端测试 |
| **MSW (Mock Service Worker)** | API Mock |

```bash
pnpm add -D vitest @vue/test-utils jsdom happy-dom @playwright/test msw
```

### 16.2 单元测试

```typescript
// tests/unit/counter.spec.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useCounterStore } from '@/stores/counter'

describe('Counter Store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })

  it('初始状态正确', () => {
    const store = useCounterStore()
    expect(store.count).toBe(0)
    expect(store.double).toBe(0)
  })

  it('increment 正确递增', () => {
    const store = useCounterStore()
    store.increment()
    expect(store.count).toBe(1)
    expect(store.double).toBe(2)
  })

  it('increment 支持参数', () => {
    const store = useCounterStore()
    store.increment(5)
    expect(store.count).toBe(5)
  })
})
```

### 16.3 组件测试

```typescript
// tests/unit/HelloWorld.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import HelloWorld from '@/components/HelloWorld.vue'

describe('HelloWorld', () => {
  it('渲染 props.title', () => {
    const wrapper = mount(HelloWorld, {
      props: { title: 'Hello Vitest' }
    })
    expect(wrapper.text()).toContain('Hello Vitest')
  })

  it('点击按钮触发 emit', async () => {
    const wrapper = mount(HelloWorld, {
      props: { title: 'Test' }
    })
    await wrapper.find('button').trigger('click')
    expect(wrapper.emitted('update')).toBeTruthy()
    expect(wrapper.emitted('update')![0]).toEqual([1, 'hello'])
  })

  it('slot 内容正确渲染', () => {
    const wrapper = mount(HelloWorld, {
      props: { title: 'Test' },
      slots: {
        default: '<p>Slot content</p>',
        header: '<h1>Header</h1>'
      }
    })
    expect(wrapper.find('p').text()).toBe('Slot content')
    expect(wrapper.find('h1').text()).toBe('Header')
  })

  it('v-model 双向绑定', async () => {
    const wrapper = mount(HelloWorld, {
      props: {
        modelValue: 'initial',
        'onUpdate:modelValue': (val: string) => wrapper.setProps({ modelValue: val })
      }
    })
    const input = wrapper.find('input')
    await input.setValue('new value')
    expect(wrapper.props('modelValue')).toBe('new value')
  })
})
```

### 16.4 E2E 测试

```typescript
// tests/e2e/home.spec.ts
import { test, expect } from '@playwright/test'

test.describe('首页', () => {
  test('正确渲染标题', async ({ page }) => {
    await page.goto('/')
    await expect(page.locator('h1')).toHaveText('Welcome to Vue App')
  })

  test('导航到关于页面', async ({ page }) => {
    await page.goto('/')
    await page.click('a[href="/about"]')
    await expect(page).toHaveURL('/about')
    await expect(page.locator('h1')).toHaveText('About')
  })

  test('表单提交', async ({ page }) => {
    await page.goto('/contact')
    await page.fill('input[name="name"]', '张三')
    await page.fill('input[name="email"]', 'zhangsan@example.com')
    await page.click('button[type="submit"]')
    await expect(page.locator('.success-message')).toBeVisible()
  })
})
```

### 16.5 测试配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'
import { fileURLToPath } from 'node:url'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  },
  test: {
    environment: 'jsdom',
    globals: true,
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html', 'lcov'],
      include: ['src/**/*.{ts,vue}'],
      exclude: ['src/**/*.d.ts', 'src/main.ts']
    }
  }
})
```

---

## 17. SSR 与 Nuxt

### 17.1 为什么需要 SSR

| 维度 | CSR（客户端渲染） | SSR（服务端渲染） |
| --- | --- | --- |
| 首屏速度 | 较慢（需下载 JS + 执行） | 快（直接输出 HTML） |
| SEO | 差（搜索引擎爬虫难以解析 JS） | 好（完整 HTML） |
| 服务器负载 | 低 | 高 |
| 交互体验 | 好（Hydration 后） | 好（Hydration 后） |
| 部署复杂度 | 低 | 高 |
| 适用场景 | 后台管理、登录后页面 | 营销页、内容站、电商 |

### 17.2 Nuxt 3 快速上手

```bash
# 创建 Nuxt 3 项目
pnpm dlx nuxi@latest init my-nuxt-app
cd my-nuxt-app
pnpm install
pnpm dev
```

```vue
<!-- app.vue -->
<template>
  <NuxtPage />
</template>
```

```vue
<!-- pages/index.vue -->
<script setup lang="ts">
// useFetch 是 Nuxt 3 的 SSR 友好请求工具
const { data: posts, pending, error } = await useFetch('/api/posts')
</script>

<template>
  <div>
    <h1>Blog</h1>
    <div v-if="pending">Loading...</div>
    <div v-else-if="error">Error</div>
    <ul v-else>
      <li v-for="post in posts" :key="post.id">
        <NuxtLink :to="`/posts/${post.id}`">{{ post.title }}</NuxtLink>
      </li>
    </ul>
  </div>
</template>
```

### 17.3 Nuxt 3 核心特性

```vue
<!-- 自动导入（无需手动 import） -->
<script setup lang="ts">
// ref, computed, watch, useRoute, useRouter, useFetch 等全部自动导入

// SEO
useHead({
  title: 'My Page',
  meta: [
    { name: 'description', content: 'Page description' }
  ]
})

// SEO 友好的 URL 参数
const route = useRoute()
const { data: product } = await useFetch(`/api/products/${route.params.id}`)
</script>
```

```typescript
// server/api/posts.ts（Nuxt 3 内建 Server API）
export default defineEventHandler(async (event) => {
  const posts = await getPosts()
  return posts
})

// server/api/posts/[id].ts
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const post = await getPostById(id)
  return post
})
```

### 17.4 Nuxt 3 与 Vue 3 的关系

Nuxt 3 是建立在 Vue 3 之上的**全栈框架**：

- 页面即路由（`pages/` 目录自动生成路由）。
- 自动导入（`ref`、`computed` 等无需手动 import）。
- 内建 SSR / SSG / ISR。
- Server API（`server/api/`）处理后端逻辑。
- 自动代码分割。

如果你的项目需要 SSR、SEO 或全栈能力，优先选择 Nuxt 3；如果是纯 CSR 的后台管理，直接用 Vue 3 + Vite 即可。

---

## 18. 工程化最佳实践

### 18.1 项目结构规范

```
src/
├── assets/                 # 静态资源（图片、字体、SVG）
├── components/             # 可复用 UI 组件
│   ├── base/               # 基础组件（Button、Input、Modal）
│   ├── layout/             # 布局组件（Header、Sidebar）
│   └── domain/             # 业务组件（UserCard、OrderTable）
├── composables/            # 组合式函数（useXxx）
├── constants/              # 常量定义
├── directives/             # 自定义指令
├── hooks/                  # 旧版 hooks（或归入 composables）
├── layouts/                # 页面布局（Nuxt）
├── middleware/             # 路由中间件（Nuxt）
├── pages/                  # 页面组件（Nuxt 自动路由）
├── plugins/                # 插件
├── router/                 # 路由配置（Vue Router）
├── services/               # API 请求封装
├── stores/                 # Pinia 状态管理
├── styles/                 # 全局样式、CSS 变量
├── types/                  # TypeScript 类型定义
├── utils/                  # 纯工具函数
├── views/                  # 页面级组件（Vue Router）
├── App.vue                 # 根组件
└── main.ts                 # 入口文件
```

### 18.2 命名规范

| 对象 | 规范 | 示例 |
| --- | --- | --- |
| 组件文件 | PascalCase | `UserProfile.vue` |
| 目录名 | kebab-case | `composables/` |
| 组合式函数 | `use` + PascalCase | `useAuth`、`useLocalStorage` |
| Pinia Store | `use` + 名称 + `Store` | `useUserStore` |
| 工具函数 | camelCase | `formatDate`、`debounce` |
| 常量 | SCREAMING_SNAKE_CASE | `API_BASE_URL` |
| CSS 类名 | kebab-case | `user-profile` |
| 事件名 | camelCase | `update:modelValue` |
| Props | camelCase | `userName`（模板中 `user-name`） |

### 18.3 代码组织建议

```vue
<script setup lang="ts">
// 1. 导入（按顺序：外部库 → 内部模块 → 组件 → 类型）
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useUserStore } from '@/stores/user'
import { formatDate } from '@/utils/date'
import UserCard from '@/components/domain/UserCard.vue'
import type { User } from '@/types/user'

// 2. 组件配置宏（defineProps / defineEmits / defineModel / defineExpose）
const props = defineProps<{ userId: string }>()
const emit = defineEmits<{ (e: 'close'): void }>()

// 3. Store
const userStore = useUserStore()

// 4. 响应式状态
const user = ref<User | null>(null)
const loading = ref(false)

// 5. 计算属性
const displayName = computed(() => user.value?.name ?? 'Unknown')

// 6. 方法
async function fetchUser() {
  loading.value = true
  try {
    user.value = await userStore.getUser(props.userId)
  } finally {
    loading.value = false
  }
}

// 7. 生命周期
onMounted(fetchUser)

// 8. Watch（如有）
// watch(() => props.userId, fetchUser)
</script>

<template>
  <!-- 模板结构清晰，避免过深嵌套 -->
</template>

<style scoped>
/* 组件样式 */
</style>
```

### 18.4 API 请求封装

```typescript
// services/http.ts
import axios, { type AxiosInstance, type AxiosRequestConfig } from 'axios'
import { useAuthStore } from '@/stores/auth'

const http: AxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
})

// 请求拦截器
http.interceptors.request.use((config) => {
  const authStore = useAuthStore()
  if (authStore.token) {
    config.headers.Authorization = `Bearer ${authStore.token}`
  }
  return config
})

// 响应拦截器
http.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      // Token 过期，跳转登录
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)

export default http

// services/user.ts
import http from './http'
import type { User, CreateUserDTO } from '@/types/user'

export const userService = {
  getAll: () => http.get<never, User[]>('/users'),
  getById: (id: string) => http.get<never, User>(`/users/${id}`),
  create: (data: CreateUserDTO) => http.post<never, User>('/users', data),
  update: (id: string, data: Partial<CreateUserDTO>) => http.put<never, User>(`/users/${id}`, data),
  delete: (id: string) => http.delete(`/users/${id}`)
}
```

### 18.5 ESLint + Prettier 配置

```json
// .eslintrc.cjs
module.exports = {
  root: true,
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'eslint:recommended',
    'plugin:vue/vue3-recommended',
    'plugin:@typescript-eslint/recommended',
    '@vue/eslint-config-prettier'
  ],
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser',
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  rules: {
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-unused-vars': 'warn',
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off'
  }
}
```

```json
// .prettierrc
{
  "semi": false,
  "singleQuote": true,
  "trailingComma": "none",
  "printWidth": 100,
  "tabWidth": 2,
  "vueIndentScriptAndStyle": true
}
```

---

## 19. 疑难排错手册

### 19.1 响应式失效

| 症状 | 原因 | 解决 |
| --- | --- | --- |
| 解构后视图不更新 | `reactive` 解构丢失响应性 | 用 `toRefs` 或改用 `ref` |
| 数组索引赋值不触发 | Vue 2 限制 | Vue 3 用 Proxy 已修复；如在 Vue 2 中用 `Vue.set` |
| 对象新增属性不触发 | Vue 2 限制 | Vue 3 已修复；如在 Vue 2 中用 `Vue.set` |
| 整体替换 `reactive` 对象丢失响应性 | `reactive` 不支持整体替换 | 用 `ref` 或 `Object.assign(state, newObj)` |
| `ref` 在模板中不更新 | 忘记 `.value` | 在 `<script setup>` 中修改需 `.value`；模板中自动解包 |

### 19.2 组件不渲染

| 症状 | 原因 | 解决 |
| --- | --- | --- |
| 组件标签被当作 HTML | 未注册组件 | 使用 `<script setup>` 自动注册，或 `components: {}` |
| `v-for` 无输出 | 数据为空 | 检查数据源 |
| `v-if` 条件永远为假 | 条件表达式错误 | 检查条件 |
| 路由不渲染 | `<RouterView />` 缺失 | 在布局组件中添加 |
| Suspense 卡住 | 异步 setup 未 resolve | 检查 `await` 的 Promise 是否正确返回 |

### 19.3 性能问题

| 症状 | 原因 | 解决 |
| --- | --- | --- |
| 列表卡顿 | 长列表未虚拟化 | 使用虚拟滚动 |
| 不必要的重新渲染 | 内联函数 / 无 key 的 v-for | 提取方法、添加 `:key` |
| 内存泄漏 | 未清理定时器 / 事件监听 | 在 `onUnmounted` 中清理 |
| 深层对象频繁更新 | 过度使用 `deep: true` | 用精确路径侦听 |
| 打包体积大 | 未代码分割 | 路由懒加载、异步组件、手动分包 |

### 19.4 TypeScript 错误

| 症状 | 原因 | 解决 |
| --- | --- | --- |
| `defineProps` 类型报错 | TS 版本不兼容 | 升级 TS 至 5.x，确保使用 `vue-tsc` |
| 模板中类型不推断 | 缺少 Volar / Vue - Official 插件 | 安装插件 |
| `ref` 类型丢失 | 未显式标注 | `ref<Type>(value)` |
| `emit` 参数类型报错 | Emits 未正确声明 | 使用类型声明方式 |

### 19.5 构建问题

| 症状 | 原因 | 解决 |
| --- | --- | --- |
| `Module not found` | 路径别名未配置 | 在 `vite.config.ts` 和 `tsconfig.json` 中配置 `alias` |
| 构建产物缺失 | `build.outDir` 配置错误 | 检查 Vite 配置 |
| 环境变量未生效 | 变量名缺少 `VITE_` 前缀 | 客户端变量必须以 `VITE_` 开头 |
| 热更新不生效 | 文件监听问题 | 重启 dev server，检查文件系统 |

### 19.6 常见错误速查

| 错误信息 | 原因 | 解决 |
| --- | --- | --- |
| `Maximum recursive updates exceeded` | 在 `watch` / `computed` 中修改了被侦听的数据 | 修正逻辑，避免循环更新 |
| `Avoid mutating a prop directly` | 子组件直接修改了 props | 用 `emit` 或 `v-model` |
| `Property 'xxx' does not exist` | 模板中使用了未定义的变量 | 检查 `<script setup>` 中是否定义 |
| `Invalid handler for event 'xxx'` | `emit` 声明与使用不一致 | 检查 Emits 类型 |
| `Hydration mismatch`（SSR） | 服务端与客户端渲染结果不同 | 避免 `Date.now()`、`Math.random()` 等非确定性内容 |
| `Cannot read properties of null` | DOM 引用为空 | 使用 `?.` 或在 `onMounted` 后访问 |

---

## 附录 A. API 速查表

### A.1 响应式 API

| API | 作用 |
| --- | --- |
| `ref(value)` | 创建响应式引用（基本类型 + 对象） |
| `reactive(obj)` | 创建响应式代理（对象/数组/Map/Set） |
| `computed(get)` | 计算属性（缓存） |
| `watch(source, cb)` | 侦听器（显式依赖） |
| `watchEffect(fn)` | 自动追踪依赖的侦听器 |
| `toRef(obj, key)` | 将对象属性转为 ref |
| `toRefs(obj)` | 将对象所有属性转为 ref |
| `toValue(source)` | 将 ref / getter / 普通值统一取值 |
| `shallowRef(value)` | 浅层 ref |
| `shallowReactive(obj)` | 浅层 reactive |
| `readonly(obj)` | 只读代理 |
| `isRef(val)` / `isReactive(val)` / `isProxy(val)` | 类型检查 |
| `markRaw(obj)` | 标记为非响应式 |
| `triggerRef(ref)` | 手动触发 ref 更新 |
| `customRef(factory)` | 自定义 ref |

### A.2 组件 API

| API | 作用 |
| --- | --- |
| `defineProps<>()` | 声明 Props |
| `defineEmits<>()` | 声明 Emits |
| `defineModel()` | v-model 双向绑定语法糖（3.4+） |
| `defineExpose()` | 暴露组件实例方法/属性 |
| `defineOptions()` | 设置组件选项（如 name） |
| `defineSlots()` | 声明插槽类型（3.3+） |
| `useSlots()` / `useAttrs()` | 获取插槽/属性 |
| `useTemplateRef()` | 模板引用（3.5+） |

### A.3 生命周期钩子

| 钩子 | 时机 |
| --- | --- |
| `onBeforeMount` | DOM 挂载前 |
| `onMounted` | DOM 挂载后 |
| `onBeforeUpdate` | DOM 更新前 |
| `onUpdated` | DOM 更新后 |
| `onBeforeUnmount` | 卸载前 |
| `onUnmounted` | 卸载后 |
| `onActivated` | KeepAlive 激活 |
| `onDeactivated` | KeepAlive 停用 |
| `onErrorCaptured` | 捕获后代组件错误 |
| `onRenderTracked` | 调试：追踪依赖 |
| `onRenderTriggered` | 调试：触发更新 |

### A.4 Vue Router 核心

| API | 作用 |
| --- | --- |
| `createRouter()` | 创建路由实例 |
| `createWebHistory()` | HTML5 History 模式 |
| `createWebHashHistory()` | Hash 模式 |
| `useRouter()` | 获取路由实例 |
| `useRoute()` | 获取当前路由 |
| `router.push()` / `router.replace()` | 编程式导航 |
| `router.back()` / `router.forward()` | 历史导航 |
| `<RouterLink>` | 声明式导航 |
| `<RouterView>` | 渲染匹配组件 |
| `router.beforeEach()` | 全局前置守卫 |
| `router.afterEach()` | 全局后置钩子 |
| `onBeforeRouteLeave` / `onBeforeRouteUpdate` | 组件内守卫 |

### A.5 Pinia 核心

| API | 作用 |
| --- | --- |
| `defineStore()` | 定义 Store |
| `storeToRefs()` | 将 Store 的 state/getters 转为 ref（保持响应性） |
| `mapState()` / `mapActions()` | Options API 映射辅助 |
| `setActivePinia()` | 设置活跃 Pinia（测试用） |
| `createPinia()` | 创建 Pinia 实例 |

---

## 附录 B. 术语表

| 术语 | 释义 |
| --- | --- |
| SFC（Single File Component） | 单文件组件（`.vue` 文件） |
| Composition API | Vue 3 的组合式 API |
| Options API | Vue 2 / Vue 3 兼容的选项式 API |
| Reactive | 响应式数据（自动追踪变化并更新视图） |
| Ref | 响应式引用（包装基本类型/对象） |
| Computed | 计算属性（缓存的派生状态） |
| Watch / WatchEffect | 侦听器 |
| Props | 父传子的属性 |
| Emits | 子传父的事件 |
| Slots | 插槽（内容分发） |
| Teleport | 将组件渲染到 DOM 任意位置 |
| Suspense | 异步组件加载状态管理 |
| KeepAlive | 组件缓存（保留状态） |
| Directive | 自定义指令（`v-` 前缀） |
| Composable | 组合式函数（`use` 前缀，复用逻辑） |
| Pinia | Vue 官方状态管理库 |
| Vue Router | Vue 官方路由库 |
| Vite | Vue 官方推荐构建工具 |
| HMR（Hot Module Replacement） | 热模块替换 |
| Hydration | 服务端渲染后客户端激活（SSR） |
| Virtual DOM | 虚拟 DOM |
| SSR / SSG / ISR | 服务端渲染 / 静态生成 / 增量静态再生 |
| Tree Shaking | 移除未使用的代码 |
| Code Splitting | 代码分割 |
| Lazy Loading | 懒加载 |
| TypeScript | 类型化 JavaScript |
| E2E | 端到端测试 |
| Volar | Vue 官方 VS Code 插件（现为 Vue - Official） |

---

## 附录 C. 延伸学习资源

1. **Vue 3 官方文档（中文）** — [cn.vuejs.org](https://cn.vuejs.org)
   **最权威的学习资料**。API 参考、指南、教程一应俱全。建议通读「指南」部分。
2. **Vue Router 官方文档** — [router.vuejs.org/zh](https://router.vuejs.org/zh)
   路由配置、导航守卫、路由懒加载的权威指南。
3. **Pinia 官方文档** — [pinia.vuejs.org/zh](https://pinia.vuejs.org/zh)
   状态管理的最佳实践与 API 参考。
4. **Vite 官方文档** — [cn.vitejs.dev](https://cn.vitejs.dev)
   构建工具配置、插件系统、优化指南。
5. **Nuxt 3 官方文档** — [nuxt.com/docs](https://nuxt.com/docs)
   全栈框架指南，SSR / SSG / Server API 的权威参考。
6. **VueUse** — [vueuse.org](https://vueuse.org)
   大量实用的组合式函数（鼠标追踪、窗口尺寸、本地存储、剪贴板等）。
7. **Vue Mastery** — [vueMastery.com](https://www.vuemastery.com)
   高质量视频课程（部分免费）。
8. **Vue.js Mastery（YouTube）** — 搜索 `Vue.js Mastery`
   免费视频教程。
9. **TypeScript 官方文档** — [typescriptlang.org](https://www.typescriptlang.org)
   TypeScript 语言参考（与 Vue 深度集成）。
10. **Vue Test Utils** — [test-utils.vuejs.org](https://test-utils.vuejs.org)
    组件测试工具库的官方文档。
11. **Vue RFCs** — [github.com/vuejs/rfcs](https://github.com/vuejs/rfcs)
    Vue 功能决策的公开讨论，了解框架演进方向。

---

> **结语**：Vue 的核心哲学是「渐进式」——从最简单的模板渲染起步，逐步引入组件化、响应式、状态管理、路由，最终构建完整的前端应用。请把核心心法记住：**响应式驱动、组合式复用、组件化封装、类型安全优先**。工具会迭代、语法会进化、生态会变迁，但「声明式描述 UI、数据变化视图自动更新、逻辑按关注点组织」这三条原则，会在任何规模、任何场景的 Vue 项目中持续生效。
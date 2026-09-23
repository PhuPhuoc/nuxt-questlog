# Middleware & Auth Guards

> **Mục tiêu:** Hiểu cách bảo vệ routes bằng middleware trong Nuxt 4.

## Mục lục

1. [Middleware là gì?](#1-middleware-là-gì)
2. [Tạo Middleware](#2-tạo-middleware)
3. [Sử dụng Middleware](#3-sử-dụng-middleware)
4. [Auth Guard Pattern](#4-auth-guard-pattern)
5. [Global Middleware](#5-global-middleware)

---

## 1. Middleware là gì?

### 1.1 Dùng để làm gì?

**Middleware = Code chạy TRƯỚC KHI page được render.**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE - GIẢI THÍCH ĐƠN GIẢN               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Middleware = Code chạy TRƯỚC KHI page được render              │
│                                                                     │
│  Dùng để:                                                         │
│  ├── Kiểm tra auth (user đã login chưa?)                     │
│  ├── Kiểm tra permissions (user có quyền không?)              │
│  ├── Redirect (chuyển hướng)                                  │
│  └── Logging, Analytics                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Có Sẵn Hay Cần Custom?

**MIDDLEWARE LÀ TÍNH NĂNG CÓ SẴN CỦA NUXT 4!**

- Tạo file trong `app/middleware/`
- Dùng `defineNuxtRouteMiddleware()` để khai báo
- Nuxt tự động chạy trước khi render page

### 1.3 Cơ Chế Hoạt Động - Behind The Scenes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE HOẠT ĐỘNG NHƯ THẾ NÀO?            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. USER NAVIGATE                                                 │
│     User click link hoặc navigateTo('/admin')                   │
│                                                                     │
│  2. MIDDLEWARE CHẠY TRƯỚC                                        │
│     ├── Global middleware (auth.global.ts)                        │
│     ├── Route middleware (auth.ts)                               │
│     └── Inline middleware                                         │
│                                                                     │
│  3. KIỂM TRA CONDITION                                           │
│     if (!isAuth) return navigateTo('/login')                   │
│                                                                     │
│  4. CHO phép HOẶC Redirect                                        │
│     ├── Pass → Render page                                       │
│     └── Redirect → Navigate đến trang khác                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.4 So Sánh Vue Router vs Nuxt Middleware

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE ROUTER vs NUXT MIDDLEWARE                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE ROUTER:                                                      │
│  ───────────────                                                   │
│  router.beforeEach((to, from, next) => {                        │
│    if (to.meta.requiresAuth && !isAuth) {                        │
│      next('/login')                                               │
│    }                                                             │
│  })                                                              │
│                                                                     │
│  NUXT MIDDLEWARE:                                                 │
│  ─────────────────                                                │
│  // app/middleware/auth.ts                                       │
│  export default defineNuxtRouteMiddleware((to, from) => {       │
│    if (to.meta.requiresAuth && !isAuth) {                        │
│      return navigateTo('/login')                                  │
│    }                                                             │
│  })                                                              │
│                                                                     │
│  → CÚ PHÁP KHÁC, NHƯNG CÙNG MỤC ĐÍCH!                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Tạo Middleware

### 2.1 Cấu trúc thư mục

```
📁 app/
└── 📁 middleware/
    ├── 📄 auth.ts           ← Named middleware
    ├── 📄 auth.global.ts   ← Global middleware (chạy mọi route)
    └── 📄 admin.ts         ← Admin-only middleware
```

### 2.2 Auth Middleware

```ts
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // Lấy user từ auth store
  const user = useUserStore()

  // Kiểm tra nếu route cần auth
  if (to.meta.requiresAuth && !user.isLoggedIn) {
    // Redirect đến login
    return navigateTo('/login')
  }

  // Nếu đã login mà vào login page thì redirect về dashboard
  if (to.path === '/login' && user.isLoggedIn) {
    return navigateTo('/dashboard')
  }
})
```

### 2.3 Admin Middleware

```ts
// app/middleware/admin.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const user = useUserStore()

  // Kiểm tra user có phải admin không
  if (!user.isAdmin) {
    // Redirect về home hoặc show 403
    return navigateTo('/')
  }
})
```

---

## 3. Sử Dụng Middleware

### 3.1 Dùng definePageMeta

```vue
<!-- app/pages/dashboard.vue -->
<script setup lang="ts">
// Áp dụng middleware cho page này
definePageMeta({
  middleware: 'auth'
})
</script>

<template>
  <div>
    <h1>Dashboard</h1>
    <p>Chào mừng, {{ user.name }}!</p>
  </div>
</template>
```

### 3.2 Multiple Middleware

```vue
<!-- app/pages/admin/settings.vue -->
<script setup lang="ts">
// Áp dụng nhiều middleware - chạy theo thứ tự
definePageMeta({
  middleware: ['auth', 'admin']
})
</script>

<template>
  <div>
    <h1>Admin Settings</h1>
  </div>
</template>
```

### 3.3 Inline Middleware

```vue
<!-- app/pages/special-page.vue -->
<script setup lang="ts">
// Inline middleware - không cần tạo file riêng
definePageMeta({
  middleware: (to, from) => {
    const isVIP = useVIPStore()

    if (!isVIP.isVIP && to.path.includes('vip')) {
      return navigateTo('/upgrade')
    }
  }
})
</script>
```

### 3.4 Route Meta

```ts
// Định nghĩa meta type cho TypeScript
// app/middleware/types.ts
declare module '#app' {
  interface PageMeta {
    requiresAuth?: boolean
    requiresAdmin?: boolean
  }
}
```

```vue
<!-- Sử dụng meta để kiểm tra -->
<script setup lang="ts">
definePageMeta({
  requiresAuth: true,
  requiresAdmin: true
})
</script>
```

```ts
// Middleware kiểm tra meta
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const user = useUserStore()

  if (to.meta.requiresAuth) {
    if (!user.isLoggedIn) {
      return navigateTo('/login')
    }
  }

  if (to.meta.requiresAdmin) {
    if (!user.isAdmin) {
      return navigateTo('/')
    }
  }
})
```

---

## 4. Auth Guard Pattern

### 4.1 Auth Store

```ts
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref<User | null>(null)
  const token = useCookie('auth_token')

  // Getters
  const isLoggedIn = computed(() => !!token.value && !!user.value)
  const isAdmin = computed(() => user.value?.role === 'admin')

  // Actions
  async function login(email: string, password: string) {
    try {
      const response = await $fetch('/api/auth/login', {
        method: 'POST',
        body: { email, password }
      })

      token.value = response.token
      user.value = response.user

      return { success: true }
    } catch (error) {
      return { success: false, error }
    }
  }

  function logout() {
    user.value = null
    token.value = null
    navigateTo('/login')
  }

  // Fetch user khi có token
  async function fetchUser() {
    if (!token.value) return

    try {
      user.value = await $fetch('/api/auth/me', {
        headers: { Authorization: `Bearer ${token.value}` }
      })
    } catch {
      logout()
    }
  }

  return {
    user,
    token,
    isLoggedIn,
    isAdmin,
    login,
    logout,
    fetchUser
  }
})
```

### 4.2 Auth Middleware Hoàn Chỉnh

```ts
// app/middleware/auth.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Routes không cần auth
  const publicRoutes = ['/login', '/register', '/forgot-password']

  // Nếu là public route, bỏ qua
  if (publicRoutes.includes(to.path)) {
    return
  }

  // Kiểm tra auth
  if (!authStore.isLoggedIn) {
    return navigateTo('/login')
  }
})
```

### 4.3 Auth Pages

```vue
<!-- app/pages/login.vue -->
<script setup lang="ts">
definePageMeta({
  layout: 'auth'
})

const form = reactive({
  email: '',
  password: ''
})

async function handleLogin() {
  const result = await useAuthStore().login(form.email, form.password)
  if (result.success) {
    navigateTo('/dashboard')
  }
}
</script>

<template>
  <form @submit.prevent="handleLogin">
    <input v-model="form.email" type="email" placeholder="Email" />
    <input v-model="form.password" type="password" placeholder="Password" />
    <button type="submit">Login</button>
  </form>
</template>
```

### 4.4 Protected Page

```vue
<!-- app/pages/dashboard.vue -->
<script setup lang="ts">
definePageMeta({
  middleware: 'auth'
})

const authStore = useAuthStore()
</script>

<template>
  <div>
    <h1>Dashboard</h1>
    <p>Chào mừng, {{ authStore.user?.name }}!</p>
  </div>
</template>
```

---

## 5. Global Middleware

### 5.1 Dùng để làm gì?

**Global middleware chạy trên MỌI route - dùng cho auth check toàn app.**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GLOBAL vs NAMED MIDDLEWARE                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NAMED MIDDLEWARE:                                                │
│  ├── File: app/middleware/auth.ts                                │
│  ├── Chỉ chạy khi page khai báo: definePageMeta({ middleware: 'auth' }) │
│  └── Ví dụ: Auth check cho dashboard                              │
│                                                                     │
│  GLOBAL MIDDLEWARE:                                                │
│  ├── File: app/middleware/auth.global.ts                          │
│  ├── Chạy TRƯỚC MỌI route                                        │
│  └── Ví dụ: Auth check toàn app, analytics, locale              │
│                                                                     │
│  ⚠️ .global.ts suffix = Global middleware!                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Tạo Global Middleware

```ts
// app/middleware/auth.global.ts
// .global.ts suffix = chạy trên MỌI route
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Public routes
  const publicRoutes = ['/login', '/register', '/forgot-password']

  if (!publicRoutes.includes(to.path)) {
    if (!authStore.isLoggedIn) {
      return navigateTo('/login')
    }
  }
})
```

### 5.3 Multiple Global Middleware

```
📁 app/middleware/
├── 📄 auth.global.ts       ← Chạy đầu tiên
├── 📄 analytics.global.ts  ← Chạy thứ hai
└── 📄 locale.global.ts    ← Chạy thứ ba
```

```ts
// app/middleware/analytics.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // Log page views
  console.log(`Page view: ${to.path}`)

  // Track analytics
  if (import.meta.client) {
    // analytics.track(to.path)
  }
})
```

```ts
// app/middleware/locale.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // Check URL locale
  const locale = to.params.lang as string

  if (!locale) {
    // Redirect to default locale
    return navigateTo('/vi')
  }
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE CHEAT SHEET                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TẠO MIDDLEWARE:                                                 │
│  ├── Named: app/middleware/auth.ts                               │
│  │   └── export default defineNuxtRouteMiddleware((to, from) => {}) │
│  └── Global: app/middleware/auth.global.ts                        │
│      └── Thêm .global.ts suffix                                   │
│                                                                     │
│  DÙNG TRONG PAGE:                                                │
│  ├── Single: definePageMeta({ middleware: 'auth' })               │
│  ├── Multiple: definePageMeta({ middleware: ['auth', 'admin'] }) │
│  └── Inline: definePageMeta({ middleware: (to, from) => {} })    │
│                                                                     │
│  REDIRECT:                                                        │
│  └── return navigateTo('/login')                                 │
│                                                                     │
│  ROUTE META:                                                     │
│  ├── Khai báo: declare module '#app' { interface PageMeta {...} }│
│  └── Sử dụng: definePageMeta({ requiresAuth: true })             │
│                                                                     │
│  THỨ TỰ CHẠY:                                                    │
│  ├── Global middleware → Route middleware → Inline middleware       │
│  └── Nếu có redirect → dừng lại, không chạy tiếp                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [06-data-fetching.md](06-data-fetching.md) - Data Fetching & SSR

hoặc → [07-state-management.md](07-state-management.md) - State Management

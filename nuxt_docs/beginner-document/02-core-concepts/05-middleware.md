# Middleware & Auth Guards

> **Mục tiêu:** Hiểu cách bảo vệ routes bằng middleware.

## Mục lục

1. [Middleware là gì?](#1-middleware-là-gì)
2. [Tạo Middleware](#2-tạo-middleware)
3. [Sử dụng Middleware](#3-sử-dụng-middleware)
4. [Auth Guard Pattern](#4-auth-guard-pattern)
5. [Global Middleware](#5-global-middleware)

---

## 1. Middleware là gì?

### Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE LÀ GÌ?                                    │
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

### So sánh Vue Router Guards và Nuxt Middleware

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE ROUTER vs NUXT MIDDLEWARE                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE ROUTER:                                                      │
│  ───────────────                                                   │
│  router.beforeEach((to, from, next) => {                         │
│    if (to.meta.requiresAuth && !isAuth) {                         │
│      next('/login')                                                │
│    }                                                              │
│  })                                                               │
│                                                                     │
│  NUXT MIDDLEWARE:                                                 │
│  ─────────────────                                                │
│  // middleware/auth.ts                                            │
│  export default defineNuxtRouteMiddleware((to, from) => {        │
│    if (to.meta.requiresAuth && !isAuth) {                         │
│      return navigateTo('/login')                                   │
│    }                                                              │
│  })                                                               │
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

```typescript
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

```typescript
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

### 3.1 Trong Page

```vue
<!-- app/pages/dashboard.vue -->
<script setup>
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
<script setup>
// Áp dụng nhiều middleware
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
<script setup>
// Inline middleware - không cần tạo file
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

```typescript
// Định nghĩa meta type
declare module '#app' {
  interface PageMeta {
    requiresAuth?: boolean
    requiresAdmin?: boolean
  }
}
```

```vue
<!-- Sử dụng meta để kiểm tra -->
<script setup>
// Kiểm tra meta trong middleware
definePageMeta({
  requiresAuth: true,
  requiresAdmin: true
})
</script>
```

```typescript
// Middleware kiểm tra meta
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  if (to.meta.requiresAuth) {
    const user = useUserStore()
    if (!user.isLoggedIn) {
      return navigateTo('/login')
    }
  }

  if (to.meta.requiresAdmin) {
    const user = useUserStore()
    if (!user.isAdmin) {
      return navigateTo('/')
    }
  }
})
```

---

## 4. Auth Guard Pattern

### 4.1 Auth Store

```typescript
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

### 4.2 Auth Middleware

```typescript
// app/middleware/auth.ts
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

### 4.3 Sử dụng trong Layout

```vue
<!-- app/layouts/default.vue -->
<script setup>
const authStore = useAuthStore()
const route = useRoute()

// Fetch user khi app mount
onMounted(async () => {
  await authStore.fetchUser()
})

const navItems = computed(() => {
  if (authStore.isLoggedIn) {
    return [
      { label: 'Trang chủ', to: '/' },
      { label: 'Dashboard', to: '/dashboard' },
      { label: 'Profile', to: '/profile' },
      { label: 'Logout', action: 'logout' }
    ]
  }

  return [
    { label: 'Trang chủ', to: '/' },
    { label: 'Login', to: '/login' }
  ]
})

function handleNavClick(item) {
  if (item.action === 'logout') {
    authStore.logout()
  }
}
</script>
```

---

## 5. Global Middleware

### 5.1 Tạo Global Middleware

```typescript
// app/middleware/auth.global.ts
// .global.ts suffix = chạy trên MỌI route
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Public routes
  const publicRoutes = ['/login', '/register']

  if (!publicRoutes.includes(to.path)) {
    if (!authStore.isLoggedIn) {
      return navigateTo('/login')
    }
  }
})
```

### 5.2 Multiple Global Middleware

```
📁 app/middleware/
├── 📄 auth.global.ts     ← Chạy đầu tiên
├── 📄 analytics.global.ts ← Chạy thứ hai
└── 📄 locale.global.ts  ← Chạy thứ ba
```

```typescript
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

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE CHEAT SHEET                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TẠO MIDDLEWARE:                                                 │
│  ├── File: app/middleware/auth.ts                                │
│  └── export default defineNuxtRouteMiddleware((to, from) => {})  │
│                                                                     │
│  DÙNG TRONG PAGE:                                                │
│  ├── definePageMeta({ middleware: 'auth' })                     │
│  ├── definePageMeta({ middleware: ['auth', 'admin'] })          │
│  └── Inline: definePageMeta({ middleware: (to, from) => {} })  │
│                                                                     │
│  GLOBAL MIDDLEWARE:                                               │
│  └── File: app/middleware/auth.global.ts (suffix .global.ts)     │
│                                                                     │
│  REDIRECT:                                                        │
│  └── return navigateTo('/login')                                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [06-data-fetching.md](06-data-fetching.md) - Data Fetching & SSR

hoặc → [07-state-management.md](07-state-management.md) - State Management

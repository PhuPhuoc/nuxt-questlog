# Middleware & Auth - Bảo Vệ Routes

> **Mục tiêu:** Học cách sử dụng middleware để bảo vệ routes và xử lý authentication.

## Mục lục

1. [Middleware là gì?](#1-middleware-là-gì)
2. [Route Middleware](#2-route-middleware)
3. [Global Middleware](#3-global-middleware)
4. [Middleware với Auth](#4-middleware-với-auth)
5. [Middleware Execution Order](#5-middleware-execution-order)
6. [Middleware Examples](#6-middleware-examples)

---

## 1. Middleware là gì?

### 1.1 Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE LÀ GÌ?                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Middleware = Code CHẠY TRƯỚC KHI page được render              │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── Auth guard: Kiểm tra login trước khi vào trang               │
│  ├── Permission check: Kiểm tra quyền admin                        │
│  ├── Redirect: Chuyển hướng based trên điều kiện                  │
│  └── Analytics: Log page visits                                    │
│                                                                     │
│  Execution flow:                                                   │
│  Request → Middleware → ... → Page Component                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Loại Middleware

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE TYPES                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. ROUTE MIDDLEWARE                                               │
│  ├── Chỉ chạy trên MỘT SỐ routes được chỉ định                  │
│  ├── Đặt trong: app/middleware/                                  │
│  └── Áp dụng bằng: definePageMeta({ middleware: 'name' })        │
│                                                                     │
│  2. GLOBAL MIDDLEWARE                                               │
│  ├── Chạy trên TẤT CẢ routes                                    │
│  ├── File name kết thúc bằng: .global.ts                         │
│  └── Tự động áp dụng cho mọi page                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Route Middleware

### 2.1 Tạo Route Middleware

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // to = route đích
  // from = route nguồn
  
  // Ví dụ: Kiểm tra auth
  const isLoggedIn = useAuthStore().isAuthenticated
  
  if (!isLoggedIn) {
    // Chuyển hướng đến login
    return navigateTo('/login')
  }
})
```

### 2.2 Áp dụng cho Page

```vue
<!-- pages/dashboard.vue -->
<script setup lang="ts">
// Áp dụng middleware 'auth'
definePageMeta({
  middleware: 'auth'
})
</script>

<template>
  <div>
    <h1>Dashboard</h1>
    <!-- Chỉ hiển thị khi đã login -->
  </div>
</template>
```

### 2.3 Multiple Middleware

```vue
<script setup lang="ts">
// Áp dụng nhiều middleware
definePageMeta({
  middleware: ['auth', 'verified']
})
</script>
```

### 2.4 Inline Middleware

```vue
<script setup lang="ts">
// Inline middleware - không cần tạo file
definePageMeta({
  middleware: [
    (to) => {
      if (to.path === '/admin') {
        return navigateTo('/login')
      }
    }
  ]
})
</script>
```

---

## 3. Global Middleware

### 3.1 Tạo Global Middleware

```typescript
// app/middleware/auth.global.ts
// .global.ts suffix = Chạy trên MỌI route

export default defineNuxtRouteMiddleware((to) => {
  // Lấy auth store
  const authStore = useAuthStore()
  
  // Routes cần auth
  const protectedRoutes = ['/dashboard', '/profile', '/settings', '/admin']
  
  // Kiểm tra nếu là protected route
  if (protectedRoutes.some(route => to.path.startsWith(route))) {
    if (!authStore.isAuthenticated) {
      return navigateTo('/login')
    }
  }
})
```

### 3.2 Global Middleware với Logging

```typescript
// app/middleware/analytics.global.ts
export default defineNuxtRouteMiddleware((to) => {
  // Log page visits
  console.log(`Navigating to: ${to.path}`)
  
  // Track page view (ví dụ với analytics)
  if (import.meta.client) {
    // Gửi analytics
  }
})
```

---

## 4. Middleware với Auth

### 4.1 Auth Store

```typescript
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)
  const token = useCookie('auth_token')
  
  const isAuthenticated = computed(() => !!token.value && !!user.value)
  const isAdmin = computed(() => user.value?.role === 'admin')
  
  async function login(credentials: LoginCredentials) {
    // Login logic
    const response = await $fetch('/api/auth/login', {
      method: 'POST',
      body: credentials
    })
    
    token.value = response.token
    user.value = response.user
    
    return response
  }
  
  function logout() {
    user.value = null
    token.value = null
    navigateTo('/login')
  }
  
  return { user, token, isAuthenticated, isAdmin, login, logout }
})
```

### 4.2 Auth Middleware

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()
  
  // Nếu chưa login, chuyển hướng đến login
  if (!authStore.isAuthenticated) {
    // Lưu URL để redirect sau khi login
    return navigateTo({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  }
})
```

### 4.3 Admin Middleware

```typescript
// app/middleware/admin.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()
  
  // Kiểm tra không chỉ login mà còn là admin
  if (!authStore.isAdmin) {
    // Không phải admin, chuyển về home
    return navigateTo('/')
  }
})
```

### 4.4 Guest Middleware (cho login/register pages)

```typescript
// app/middleware/guest.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()
  
  // Nếu đã login, không cho vào login/register
  if (authStore.isAuthenticated) {
    return navigateTo('/dashboard')
  }
})
```

### 4.5 Sử dụng Auth Middleware

```vue
<!-- pages/login.vue -->
<script setup lang="ts">
// Guest middleware - chỉ cho phép user CHƯA login
definePageMeta({
  middleware: 'guest'
})
</script>

<template>
  <div>
    <h1>Đăng Nhập</h1>
    <!-- Login form -->
  </div>
</template>
```

```vue
<!-- pages/dashboard.vue -->
<script setup lang="ts">
// Auth middleware - yêu cầu đã login
definePageMeta({
  middleware: 'auth'
})
</script>
```

```vue
<!-- pages/admin/users.vue -->
<script setup lang="ts">
// Cả auth + admin middleware
definePageMeta({
  middleware: ['auth', 'admin']
})
</script>
```

---

## 5. Middleware Execution Order

### 5.1 Order Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE EXECUTION ORDER                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Global Middleware (.global.ts)                                 │
│     └── Chạy theo thứ tự alphabet                                │
│         analytics.global.ts → auth.global.ts                       │
│                                                                     │
│  2. Page Middleware (definePageMeta)                               │
│     └── Chạy theo thứ tự trong array                              │
│         ['auth', 'admin'] → auth → admin                          │
│                                                                     │
│  3. Page Component                                                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Ví dụ Execution Flow

```typescript
// app/middleware/analytics.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  console.log('1. Analytics global')
})

// app/middleware/auth.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  console.log('2. Auth global')
})

// pages/admin/users.vue
definePageMeta({
  middleware: ['auth', 'admin']
})

// Execution:
// 1. analytics.global.ts
// 2. auth.global.ts
// 3. auth middleware (from page)
// 4. admin middleware (from page)
// 5. admin/users.vue component
```

### 5.3 Abort Navigation

```typescript
// app/middleware/maintenance.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const isMaintenanceMode = useRuntimeConfig().public.maintenanceMode
  
  if (isMaintenanceMode && to.path !== '/maintenance') {
    abortNavigation({
      statusCode: 503,
      statusMessage: 'Service Unavailable'
    })
    return navigateTo('/maintenance')
  }
})
```

---

## 6. Middleware Examples

### 6.1 Theme Middleware

```typescript
// app/middleware/theme.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const colorMode = useColorMode()
  
  // Đọc theme từ query hoặc cookie
  if (to.query.theme) {
    colorMode.preference = to.query.theme as 'light' | 'dark'
  }
})
```

### 6.2 Locale Middleware

```typescript
// app/middleware/locale.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const { locale, setLocale } = useI18n()
  
  // Kiểm tra locale trong URL
  const locales = ['en', 'vi', 'ja']
  const urlLocale = to.path.split('/')[1]
  
  if (locales.includes(urlLocale)) {
    setLocale(urlLocale)
  }
})
```

### 6.3 Subscription Check Middleware

```typescript
// app/middleware/subscription.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()
  
  // Routes cần subscription
  const premiumRoutes = ['/premium', '/courses', '/mentor']
  
  if (premiumRoutes.some(route => to.path.startsWith(route))) {
    // Kiểm tra subscription
    if (!authStore.user?.hasSubscription) {
      return navigateTo('/upgrade')
    }
  }
})
```

### 6.4 Route Meta với Middleware

```typescript
// app/middleware/role-guard.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()
  
  // Lấy required role từ route meta
  const requiredRole = to.meta.requiredRole as string | undefined
  
  if (requiredRole && authStore.user?.role !== requiredRole) {
    return navigateTo('/unauthorized')
  }
})
```

```vue
<!-- pages/admin.vue -->
<script setup lang="ts">
// Định nghĩa role yêu cầu trong route meta
definePageMeta({
  middleware: 'role-guard',
  meta: {
    requiredRole: 'admin'
  }
})
</script>
```

### 6.5 Redirect Middleware

```typescript
// app/middleware/redirects.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // Old URLs redirect
  const redirects: Record<string, string> = {
    '/old-page': '/new-page',
    '/blog-old': '/blog',
    '/products': '/shop'
  }
  
  const redirectPath = redirects[to.path]
  if (redirectPath) {
    return navigateTo(redirectPath, { redirectCode: 301 })
  }
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE CHEAT SHEET                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TYPES:                                                            │
│  ├── Route Middleware: Chỉ chạy trên một số routes                │
│  └── Global Middleware: Chạy trên mọi route (.global.ts)         │
│                                                                     │
│  FILE NAMING:                                                      │
│  ├── auth.ts → apply với definePageMeta                          │
│  └── auth.global.ts → auto-apply cho mọi route                   │
│                                                                     │
│  API:                                                              │
│  ├── defineNuxtRouteMiddleware((to, from) => {})                 │
│  ├── navigateTo('/path') → chuyển hướng                         │
│  ├── abortNavigation() → hủy navigation                           │
│  └── to.meta → route metadata                                     │
│                                                                     │
│  USAGE:                                                            │
│  definePageMeta({ middleware: 'auth' })                           │
│  definePageMeta({ middleware: ['auth', 'admin'] })                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [06-data-fetching.md](07-data-fetching.md) - Data Fetching

hoặc → [07-state-management.md](08-state-management.md) - State Management

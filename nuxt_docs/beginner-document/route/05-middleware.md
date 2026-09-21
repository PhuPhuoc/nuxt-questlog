# Chapper 5: Middleware - Route Guards

## Mục lục

1. [Middleware là gì?](#1-middleware-là-gì)
2. [Ba Loại Middleware](#2-ba-loại-middleware)
3. [Auth Middleware Thực Tế](#3-auth-middleware-thực-tế)
4. [Execution Order](#4-execution-order)
5. [SSR vs Client Navigation](#5-ssr-vs-client-navigation)

---

## 1. Middleware là gì?

### Khái Niệm

Middleware trong Nuxt tương đương với **Navigation Guards** trong Vue Router. Nó cho phép bạn chạy code **TRƯỚC KHI** user vào một trang.

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE FLOW                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  User click /admin                                                │
│           ↓                                                        │
│  ┌──────────────────┐                                            │
│  │   Middleware    │  ← Kiểm tra auth, permissions              │
│  │   chạy ở đây   │     trước khi render page                 │
│  └────────┬─────────┘                                            │
│           ↓                                                        │
│  ┌──────────────────┐                                            │
│  │  Pass?           │                                            │
│  └────────┬─────────┘                                            │
│           ↓                                                        │
│     ┌─────┴─────┐                                                │
│     ↓           ↓                                                │
│   ✅ YES       ❌ NO                                              │
│     ↓           ↓                                                │
│  Render     Redirect                                              │
│  Page       /login                                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Use Cases Phổ Biến

| Use Case | Mô tả |
|---------|--------|
| **Auth Check** | Kiểm tra user đã đăng nhập chưa |
| **Role Check** | Kiểm tra user có quyền truy cập không |
| **Theme/Locale** | Set theme hoặc ngôn ngữ |
| **Analytics** | Track page views |
| **Feature Flags** | Kiểm tra feature có enable không |

---

## 2. Ba Loại Middleware

### 2.1 Anonymous Middleware (Inline)

Định nghĩa ngay trong component:

```vue
<script setup>
definePageMeta({
  middleware: [
    (to, from) => {
      // Kiểm tra gì đó
      if (!isAllowed()) {
        return navigateTo('/access-denied')
      }
    }
  ]
})
</script>
```

### 2.2 Named Middleware (File)

Tạo file trong thư mục `middleware/`:

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const auth = useAuth()

  if (!auth.isLoggedIn) {
    return navigateTo('/login')
  }
})
```

Sử dụng trong page:

```vue
<script setup>
definePageMeta({
  middleware: 'auth'  // Chỉ cần tên file, không cần .ts
})
</script>
```

### 2.3 Global Middleware

Thêm đuôi `.global.ts` hoặc đặt trong thư mục con `global/`:

```typescript
// app/middleware/auth.global.ts
// hoặc
// app/middleware/global/auth.ts

export default defineNuxtRouteMiddleware((to, from) => {
  // Chạy trên MỌI route
  console.log('Navigating to:', to.path)
})
```

---

## 3. Auth Middleware Thực Tế

### Cấu Trúc Thư Mục

```
📁 app/
├── 📁 middleware/
│   ├── 📄 auth.ts           ← Named middleware
│   ├── 📄 auth.global.ts  ← Global middleware
│   ├── 📄 admin.ts          ← Role-based
│   └── 📄 guest.ts          ← Chỉ cho guest
├── 📁 stores/
│   └── 📄 auth.ts           ← Auth store
└── ...
```

### Auth Store

```typescript
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const user = ref<{ id: string; name: string; role: string } | null>(null)
  const token = useCookie('auth_token')

  const isLoggedIn = computed(() => !!user.value && !!token.value)
  const isAdmin = computed(() => user.value?.role === 'admin')

  async function login(email: string, password: string) {
    const response = await $fetch('/api/auth/login', {
      method: 'POST',
      body: { email, password }
    })
    user.value = response.user
    token.value = response.token
  }

  async function logout() {
    await $fetch('/api/auth/logout', { method: 'POST' })
    user.value = null
    token.value = null
  }

  return { user, token, isLoggedIn, isAdmin, login, logout }
})
```

### Auth Middleware (Named)

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Routes không cần auth
  const publicRoutes = ['/login', '/register', '/forgot-password']

  if (publicRoutes.includes(to.path)) {
    return  // Skip, cho phép access
  }

  // Kiểm tra đăng nhập
  if (!authStore.isLoggedIn) {
    return navigateTo({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  }
})
```

### Admin Middleware (Role-based)

```typescript
// app/middleware/admin.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  if (!authStore.isLoggedIn) {
    return navigateTo('/login')
  }

  // Role hierarchy: user < admin < superadmin
  if (!authStore.isAdmin) {
    return navigateTo('/403')
  }
})
```

### Guest Middleware (Chỉ Cho Chưa Đăng Nhập)

```typescript
// app/middleware/guest.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Nếu đã đăng nhập, redirect đi
  if (authStore.isLoggedIn) {
    return navigateTo('/dashboard')
  }
})
```

### Sử Dụng Trong Pages

```vue
<!-- app/pages/dashboard.vue -->
<script setup>
// Chỉ auth users mới vào được
definePageMeta({
  middleware: 'auth'
})
</script>
```

```vue
<!-- app/pages/admin/users.vue -->
<script setup>
// Chỉ admin mới vào được
definePageMeta({
  middleware: ['auth', 'admin']
})
</script>
```

```vue
<!-- app/pages/login.vue -->
<script setup>
// Chỉ guest (chưa đăng nhập) mới vào được
definePageMeta({
  middleware: 'guest'
})
</script>
```

---

## 4. Execution Order

### Thứ Tự执行

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE EXECUTION ORDER                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Global Middleware (theo thứ tự alphabet)                      │
│     ├── analytics.global.ts                                         │
│     ├── auth.global.ts                                             │
│     └── locale.global.ts                                           │
│                         ↓                                           │
│  2. Layout Middleware (trong definePageMeta của layout)            │
│                         ↓                                           │
│  3. Page Middleware (trong definePageMeta của page)                │
│     ├── 'auth' ──→ auth.ts                                        │
│     └── 'admin' ──→ admin.ts                                      │
│                         ↓                                           │
│  4. Page Component                                                 │
│                                                                     │
│  ⚠️ NẾU BẤT KỲ MIDDLEWARE NÀO RETURN navigateTo/abort:          │
│     → Luồng DỪNG LẠI, các bước sau KHÔNG chạy                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Ví Dụ: Multiple Middleware

```vue
<!-- app/pages/admin/settings.vue -->
<script setup>
// Chạy theo thứ tự: auth.ts → admin.ts
definePageMeta({
  middleware: ['auth', 'admin']
})
</script>
```

### abortNavigation

```typescript
// Dùng abortNavigation() để từ chối mà không redirect
export default defineNuxtRouteMiddleware((to, from) => {
  if (!hasPermission) {
    // abortNavigation() không redirect, chỉ từ chối
    return abortNavigation()
  }

  // Hoặc với message
  return abortNavigation({
    statusCode: 403,
    statusMessage: 'You do not have permission'
  })
})
```

### Return Values

```typescript
// 1. Return undefined/null = cho phép navigation
return

// 2. Return navigateTo() = redirect
return navigateTo('/login')

// 3. Return abortNavigation() = từ chối không redirect
return abortNavigation()

// 4. Return false = (trong Vue Router) hủy navigation
//    KHÔNG khuyến khích trong Nuxt
```

---

## 5. SSR vs Client Navigation

### SSR Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SSR NAVIGATION                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Browser Request /dashboard                                        │
│           ↓                                                        │
│  Server receives request                                          │
│           ↓                                                        │
│  Middleware chạy trên server                                      │
│           ↓                                                        │
│  ┌──────────────────┐                                            │
│  │ Auth check      │ ← Có thể redirect ở đây                     │
│  └────────┬─────────┘                                            │
│           ↓                                                        │
│  ┌──────────────────┐                                            │
│  │ Fetch data      │ ← useFetch chạy song song                   │
│  └────────┬─────────┘                                            │
│           ↓                                                        │
│  Server trả HTML + redirect header (nếu có)                      │
│           ↓                                                        │
│  Browser nhận response                                           │
│           ↓                                                        │
│  Nếu redirect: Browser chuyển trang                               │
│  Nếu không: Render HTML + Hydrate                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Client-Side Navigation

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CLIENT NAVIGATION                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  User click <NuxtLink>                                            │
│           ↓                                                        │
│  Middleware chạy trên client                                       │
│           ↓                                                        │
│  ┌──────────────────┐                                            │
│  │ Auth check      │ ← Có thể redirect ở đây                     │
│  └────────┬─────────┘                                            │
│           ↓                                                        │
│  useFetch/useAsyncData (nếu không lazy)                            │
│           ↓                                                        │
│  Update DOM (SPA - không reload)                                   │
│           ↓                                                        │
│  router.afterEach hooks                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Global Middleware Ví Dụ

```typescript
// app/middleware/auth.global.ts
// Global auth check - chạy trên MỌI route

export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Skip cho routes không cần auth
  if (isPublicRoute(to.path)) {
    return
  }

  // Skip cho API routes và static files
  if (to.path.startsWith('/api') || to.path.startsWith('/_nuxt')) {
    return
  }

  // Kiểm tra auth
  if (!authStore.isLoggedIn) {
    return navigateTo({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  }
})

function isPublicRoute(path: string): boolean {
  const publicRoutes = [
    '/',
    '/login',
    '/register',
    '/forgot-password',
    '/about',
    '/blog'
  ]
  return publicRoutes.some(route =>
    path === route || path.startsWith(route + '/')
  )
}
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIDDLEWARE SUMMARY                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TYPES:                                                           │
│  ──────                                                           │
│  Anonymous:   inline trong definePageMeta                         │
│  Named:       file trong middleware/                               │
│  Global:      file.global.ts trong middleware/                    │
│                                                                     │
│  USAGE:                                                          │
│  ──────                                                          │
│  definePageMeta({ middleware: 'auth' })                           │
│  definePageMeta({ middleware: ['auth', 'admin'] })                │
│  definePageMeta({ middleware: [(to) => {}] })                    │
│                                                                     │
│  RETURN VALUES:                                                   │
│  ─────────────                                                   │
│  return              → Cho phép navigation                          │
│  return navigateTo() → Redirect                                    │
│  return abortNavigation() → Từ chối                                │
│                                                                     │
│  ORDER:                                                           │
│  ──────                                                           │
│  Global → Layout → Page middleware                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập

### Bài 1: Auth Middleware Hoàn Chỉnh

Tạo hệ thống auth middleware:

1. `middleware/auth.ts` - Kiểm tra đăng nhập
2. `middleware/admin.ts` - Kiểm tra admin role
3. `middleware/guest.ts` - Chỉ cho guest
4. Áp dụng vào các pages

### Bài 2: Role-based Access

Mở rộng auth middleware để hỗ trợ:

- `user` - User thường
- `admin` - Quản trị viên
- `superadmin` - Super admin

### Bài 3: Page Analytics Middleware

Tạo global middleware để:

1. Track page views
2. Gửi analytics event

---

## ▶️ Tiếp Theo

→ [Chapper 6: Advanced](06-advanced.md) - Học về route validation, interceptors, lifecycle

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

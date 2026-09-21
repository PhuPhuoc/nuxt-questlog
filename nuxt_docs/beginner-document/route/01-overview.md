# Chapper 1: Tổng Quan - Nuxt Routing vs Vue Router

## Mục lục

1. [Tại sao Nuxt có File-based Routing?](#1-tại-sao-nuxt-có-file-based-routing)
2. [So sánh Vue Router vs Nuxt Routing](#2-so-sánh-vue-router-vs-nuxt-routing)
3. [Các Khái Niệm Cốt Lõi](#3-các-khái-niệm-cốt-lõi)

---

## 1. Tại Sao Nuxt Có File-based Routing?

### Vue Router Thuần - Cấu Hình Thủ Công

```typescript
// src/router/index.ts
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'
import About from '../views/About.vue'
import UserProfile from '../views/UserProfile.vue'

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/user/:id', component: UserProfile }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

**Vấn đề:**
- ❌ Phải import từng component
- ❌ Dễ quên thêm route mới
- ❌ Khó quản lý khi project lớn
- ❌ Phải tự cấu hình nested routes

### Nuxt - Tự Động Từ File

```
📁 app/pages/
├── 📄 index.vue    →  /
├── 📄 about.vue    →  /about
└── 📄 user/
    └── 📄 [id].vue →  /user/:id
```

**Lợi ích:**
- ✅ Không cần cấu hình router
- ✅ Route = File path
- ✅ Tự động code splitting
- ✅ Nested routes = thư mục

---

## 2. So Sánh Vue Router vs Nuxt Routing

### Bảng So Sánh Chi Tiết

| Khía cạnh | Vue Router | Nuxt Routing |
|-----------|------------|--------------|
| **Cách định nghĩa Route** | Cấu hình thủ công | File trong `pages/` |
| **Dynamic Routes** | `path: '/user/:id'` | `[id].vue` |
| **Nested Routes** | `children: []` | Thư mục con |
| **Navigation Guards** | `router.beforeEach()` | `middleware/*.ts` |
| **Layouts** | Tự xây dựng | Hỗ trợ sẵn |
| **Code Splitting** | Tự cấu hình | Tự động |
| **SSR Support** | Cần cấu hình thêm | Tích hợp sẵn |

### So Sánh Cú Pháp

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPARISON TABLE                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NAVIGATION                                                         │
│  ─────────────────────────────────────────────────────────────────  │
│  Vue Router:  router.push('/about')                                 │
│  Nuxt:       navigateTo('/about')    ← Đơn giản hơn!             │
│                                                                     │
│  LINK COMPONENT                                                      │
│  ─────────────────────────────────────────────────────────────────  │
│  Vue Router:  <router-link to="/about">                            │
│  Nuxt:       <NuxtLink to="/about">   ← Ngắn hơn!               │
│                                                                     │
│  PAGE RENDERING                                                      │
│  ─────────────────────────────────────────────────────────────────  │
│  Vue Router:  <router-view />                                       │
│  Nuxt:       <NuxtPage />            ← Tự động!                  │
│                                                                     │
│  DYNAMIC SEGMENT                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│  Vue Router:  path: '/user/:id'                                    │
│  Nuxt:       user/[id].vue          ← File name!                 │
│                                                                     │
│  NAVIGATION GUARD                                                    │
│  ─────────────────────────────────────────────────────────────────  │
│  Vue Router:  router.beforeEach((to, from, next) => {})           │
│  Nuxt:       export default defineNuxtRouteMiddleware((to) => {})  │
│                                                                     │
│  ROUTE META                                                         │
│  ─────────────────────────────────────────────────────────────────  │
│  Vue Router:  { path: '/admin', meta: { requiresAuth: true } }    │
│  Nuxt:       definePageMeta({ middleware: 'auth' })              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Ví Dụ Code Song Song

#### Vue Router

```typescript
// router/index.ts
const routes = [
  {
    path: '/',
    name: 'home',
    component: () => import('../views/Home.vue')
  },
  {
    path: '/user/:id',
    name: 'user-profile',
    component: () => import('../views/UserProfile.vue'),
    children: [
      {
        path: 'posts',
        component: () => import('../views/UserPosts.vue')
      }
    ]
  }
]

router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !isAuth()) {
    next('/login')
  } else {
    next()
  }
})
```

#### Nuxt

```
📁 app/
├── pages/
│   ├── index.vue
│   └── user/
│       ├── [id].vue
│       └── [id]/
│           └── posts.vue
└── middleware/
    └── auth.ts
```

```typescript
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to) => {
  if (!isAuth() && to.path !== '/login') {
    return navigateTo('/login')
  }
})
```

---

## 3. Các Khái Niệm Cốt Lõi

### 3.1 File Path = Route Path

| File | URL |
|------|-----|
| `pages/index.vue` | `/` |
| `pages/about.vue` | `/about` |
| `pages/blog/index.vue` | `/blog` |
| `pages/blog/[slug].vue` | `/blog/:slug` |
| `pages/[...catchall].vue` | `/*` |

### 3.2 Route Resolution

```
URL: /user/123/posts

1. Tìm pages/user/[id].vue
2. Tìm pages/user/[id]/posts.vue (nested)
3. Render với <NuxtPage />
```

### 3.3 Auto-imports

Trong Nuxt, bạn KHÔNG cần import:

```vue
<!-- Vue Router - phải import -->
<script setup>
import { useRoute, useRouter } from 'vue-router'
const route = useRoute()
const router = useRouter()
</script>

<!-- Nuxt - tự động có sẵn -->
<script setup>
// Không cần import!
const route = useRoute()
const router = useRouter()
</script>
```

### 3.4 Route Meta vs Page Meta

```typescript
// Vue Router
route.meta.requiresAuth  // Access meta data

// Nuxt
definePageMeta({
  middleware: 'auth',
  layout: 'default',
  title: 'My Page'
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KEY TAKEAWAYS                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ File-based routing = file path = route path                    │
│  ✅ Không cần cấu hình router - Nuxt tự làm                     │
│  ✅ Auto-imports - useRoute, useRouter có sẵn                    │
│  ✅ <NuxtLink> thay thế <router-link>                            │
│  ✅ <NuxtPage> thay thế <router-view>                            │
│  ✅ Middleware thay thế navigation guards                          │
│  ✅ Tự động code splitting                                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập Nhanh

### Câu Hỏi

1. Làm thế nào để tạo route `/blog` trong Nuxt?
2. `<NuxtLink>` tương đương với gì trong Vue Router?
3. Làm thế nào để tạo dynamic route `/user/:id`?
4. Ưu điểm chính của file-based routing là gì?

### Đáp Án

1. Tạo file `pages/blog.vue` hoặc `pages/blog/index.vue`
2. `<router-link>` trong Vue Router
3. Tạo file `pages/user/[id].vue`
4. Không cần cấu hình thủ công, tự động code splitting

---

## ▶️ Tiếp Theo

→ [Chapper 2: Basic Routes](02-basic-routes.md) - Học cách tạo các loại route

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

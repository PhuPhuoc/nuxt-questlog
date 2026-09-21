# Chapper 4: Navigation - Di Chuyển Giữa Các Trang

## Mục lục

1. [NuxtLink - Link Thông Minh](#1-nuxtlink---link-thông-minh)
2. [navigateTo - Chuyển Hướng](#2-navigateto---chuyển-hướng)
3. [useRouter - Router Composable](#3-userouter---router-composable)
4. [useRoute - Lấy Thông Tin Route](#4-useroute---lấy-thông-tin-route)

---

## 1. NuxtLink - Link Thông Minh

### NuxtLink vs HTML Anchor

```vue
<!-- HTML - reload toàn bộ page -->
<a href="/about">About</a>

<!-- NuxtLink - SPA navigation, KHÔNG reload -->
<NuxtLink to="/about">About</NuxtLink>
```

### Tính Năng Của NuxtLink

| Tính năng | Mô tả |
|-----------|--------|
| **Prefetching** | Tự động prefetch trang khi link hiện trong viewport |
| **Active State** | Class `router-link-active` khi route active |
| **Exact Active** | Class `router-link-exact-active` khi khớp chính xác |
| **SPA Navigation** | Không reload page, chỉ thay đổi nội dung |
| **External Detection** | Tự động nhận biết external links |

### Cú Pháp Cơ Bản

```vue
<template>
  <nav>
    <!-- Link đơn giản -->
    <NuxtLink to="/">Trang chủ</NuxtLink>

    <!-- Dynamic link -->
    <NuxtLink :to="`/user/${userId}`">
      Profile của tôi
    </NuxtLink>

    <!-- Query params -->
    <NuxtLink :to="{ path: '/blog', query: { category: 'vue' } }">
      Blog Vue
    </NuxtLink>

    <!-- Replace history (không lưu vào history) -->
    <NuxtLink to="/splash" replace>
      Go (replace)
    </NuxtLink>

    <!-- External link -->
    <NuxtLink to="https://google.com" target="_blank">
      Google
    </NuxtLink>
  </nav>
</template>
```

### Active State Styling

```vue
<style scoped>
/* Khi route bắt đầu với /blog */
.router-link-active {
  color: #42b883;
  font-weight: bold;
}

/* Khi route KHỚP CHÍNH XÁC /blog */
.router-link-exact-active {
  color: #35495e;
  border-bottom: 2px solid #42b883;
}
</style>
```

### Prefetch Control

```vue
<template>
  <!-- Prefetch mặc định - tự động prefetch khi visible -->
  <NuxtLink to="/heavy-page">Trang nặng</NuxtLink>

  <!-- Tắt prefetch -->
  <NuxtLink to="/page" no-prefetch>
    Không prefetch
  </NuxtLink>

  <!-- Prefetch khi hover -->
  <NuxtLink to="/page" prefetch-on="interaction">
    Prefetch on hover
  </NuxtLink>
</template>
```

### External Links

```vue
<template>
  <!-- External link - tự động nhận biết -->
  <NuxtLink to="https://github.com" external target="_blank">
    GitHub
  </NuxtLink>

  <!--
    Lưu ý: external links KHÔNG có:
    - Active state
    - Prefetching
    - SPA navigation
  -->
</template>
```

---

## 2. navigateTo - Chuyển Hướng

### Giới Thiệu navigateTo

`navigateTo` là một utility function toàn cục của Nuxt, KHÔNG cần import:

```typescript
// Đây là global utility - không cần import!
await navigateTo('/dashboard')

// Tương đương với:
const router = useRouter()
await router.push('/dashboard')
```

### Cú Pháp

```typescript
// Chuyển hướng đơn giản
await navigateTo('/about')

// Replace current entry (không lưu vào history)
await navigateTo('/home', { replace: true })

// Với query params
await navigateTo({ path: '/search', query: { q: 'vue' } })

// Chuyển hướng ra ngoài
await navigateTo('https://google.com')

// Với hash
await navigateTo('/page#section')
```

### Ví Dụ Trong Actions

```typescript
// Trong một action/composable
async function login(credentials) {
  try {
    await $fetch('/api/auth/login', { method: 'POST', body: credentials })
    // Đăng nhập thành công → chuyển về dashboard
    await navigateTo('/dashboard')
  } catch (error) {
    // Xử lý lỗi
  }
}

async function logout() {
  await $fetch('/api/auth/logout', { method: 'POST' })
  // Đăng xuất → chuyển về trang chủ
  await navigateTo('/')
}
```

### Ví Dụ Trong Events

```vue
<script setup>
const handleSubmit = async () => {
  await saveData()
  await navigateTo('/success')
}

const handleCancel = () => {
  navigateTo('/')
}
</script>

<template>
  <div>
    <button @click="handleSubmit">Lưu và tiếp tục</button>
    <button @click="handleCancel">Hủy</button>
  </div>
</template>
```

### Navigate Với Object Syntax

```typescript
// Với path và query
await navigateTo({
  path: '/blog',
  query: { category: 'vue', page: 1 }
})
// URL: /blog?category=vue&page=1

// Với path và hash
await navigateTo({
  path: '/page',
  hash: '#section-2'
})
// URL: /page#section-2

// Với path và external (nếu có)
await navigateTo('https://external-site.com', { external: true })
```

### Error Handling

```typescript
// navigateTo có thể throw error trong một số trường hợp
try {
  await navigateTo('/protected')
} catch (error) {
  // Xử lý khi navigation thất bại
  console.error('Navigation failed:', error)
}
```

---

## 3. useRouter - Router Composable

### Giới Thiệu

`useRouter` trả về router instance, tương tự Vue Router:

```typescript
const router = useRouter()
```

### Các Methods

```typescript
const router = useRouter()

// Điều hướng
router.push('/destination')      // Thêm vào history
router.replace('/destination')   // Thay thế current
router.back()                   // Quay lại
router.forward()               // Đi tới
router.go(-2)                  // Đi 2 bước trong history

// Resolve route
const resolved = router.resolve('/user/123')
// resolved.href = '/user/123'
// resolved.params = { id: '123' }
```

### Ví Dụ

```vue
<script setup>
const router = useRouter()

// Push navigation
function goToUser(id) {
  router.push(`/user/${id}`)
}

// Replace navigation
function showSplash() {
  router.replace('/splash-screen')
}

// Go back
function handleBack() {
  if (router.canGoBack()) {
    router.back()
  } else {
    router.push('/')
  }
}

// Dynamic navigation
async function filterByCategory(category) {
  router.push({
    path: '/blog',
    query: { category }
  })
}
</script>
```

### Navigation Hooks

```vue
<script setup>
const router = useRouter()

// Trước khi navigate
router.beforeEach((to, from) => {
  console.log('Navigating to:', to.path)
  // Có thể return false để cancel
  // Hoặc return '/alternative-path' để redirect
})

// Sau khi navigate
router.afterEach((to, from, failure) => {
  if (failure) {
    console.error('Navigation failed:', failure)
  } else {
    console.log('Navigated to:', to.path)
  }
})

// On error
router.onError((error) => {
  console.error('Router error:', error)
})
</script>
```

### So Sánh navigateTo vs useRouter

```
┌─────────────────────────────────────────────────────────────────────┐
│                    navigateTo vs useRouter                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  navigateTo()                                                        │
│  ────────────                                                       │
│  • Global utility - KHÔNG cần import                              │
│  • Tự động handle SSR redirect                                     │
│  • Có thể navigate ra external URL                                 │
│  • Throw error nếu navigation thất bại                              │
│  • Async by default                                                │
│                                                                     │
│  useRouter()                                                        │
│  ────────────                                                       │
│  • Composable - cần call trong setup                               │
│  • Truy cập router hooks (beforeEach, afterEach)                   │
│  • Resolve route paths                                              │
│  • Kiểm tra canGoBack()                                            │
│                                                                     │
│  KHI NÀO DÙNG:                                                     │
│  ─────────────                                                       │
│  ✅ Dùng navigateTo() cho điều hướng thông thường                  │
│  ✅ Dùng useRouter() khi cần hooks hoặc canGoBack()                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. useRoute - Lấy Thông Tin Route

### Giới Thiệu

`useRoute` trả về current route object:

```typescript
const route = useRoute()
```

### Các Properties

```typescript
const route = useRoute()

route.path      // '/user/123'
route.name     // 'user-profile'
route.params   // { id: '123' }
route.query    // { tab: 'posts' }
route.hash     // '#section'
route.fullPath // '/user/123?tab=posts#section'
route.meta     // { requiresAuth: true }
route.href     // '/user/123?tab=posts'
```

### Ví Dụ: Đọc URL Parameters

```vue
<script setup>
const route = useRoute()

// Dynamic param
const userId = route.params.id

// Query params
const tab = route.query.tab || 'posts'
const page = Number(route.query.page) || 1

// Fetch data dựa trên params
const { data: user } = await useFetch(`/api/users/${userId}`)
</script>
```

### Route Params Types

```typescript
// pages/user/[id].vue
// URL: /user/123

route.params.id    // '123' (string)

// pages/blog/[year]/[month].vue
// URL: /blog/2024/03

route.params.year   // '2024'
route.params.month  // '03'

// pages/[...slug].vue
// URL: /docs/getting-started/installation

route.params.slug  // ['docs', 'getting-started', 'installation']
```

### Query Parameters

```typescript
// URL: /search?q=vue&category=frontend&page=2

route.query.q         // 'vue'
route.query.category  // 'frontend'
route.query.page      // '2' (string, cần convert)

// Chuyển đổi type
const page = computed(() => Number(route.query.page) || 1)
```

### Watch Route Changes

```typescript
// Tự động chạy khi route thay đổi
watch(
  () => route.params.id,
  (newId, oldId) => {
    console.log(`User changed from ${oldId} to ${newId}`)
    // Fetch new data
  }
)

// Hoặc dùng watchEffect
watchEffect(() => {
  console.log('Current path:', route.path)
  console.log('Query:', route.query)
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NAVIGATION SUMMARY                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NuxtLink:                                                          │
│  ────────                                                           │
│  <NuxtLink to="/page">          Basic link                        │
│  <NuxtLink :to="path">         Dynamic link                      │
│  no-prefetch                    Disable prefetch                  │
│  active-class                   Custom active class               │
│                                                                     │
│  navigateTo:                                                        │
│  ──────────                                                         │
│  await navigateTo('/page')       Simple redirect                  │
│  await navigateTo('/', { replace: true })  Replace entry          │
│  await navigateTo({ path, query })  With params                   │
│                                                                     │
│  useRouter:                                                        │
│  ──────────                                                        │
│  router.push()                  Navigate                         │
│  router.back()                  Go back                         │
│  router.beforeEach()            Navigation hooks                   │
│                                                                     │
│  useRoute:                                                         │
│  ──────────                                                        │
│  route.params.id               URL params                        │
│  route.query.tab               Query params                      │
│  route.path                    Current path                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập

### Bài 1: Navigation Component

Tạo component `AppNav.vue` với:

1. Links: Home, Blog, About, Contact
2. Active state styling
3. Mobile responsive menu

### Bài 2: Pagination

Tạo component với:

```vue
<script setup>
const route = useRoute()
const router = useRouter()

const currentPage = computed(() => Number(route.query.page) || 1)

function goToPage(page) {
  router.push({
    path: '/blog',
    query: { ...route.query, page }
  })
}
</script>
```

### Bài 3: Search Form

Tạo form search với:

1. Input field
2. Submit → navigateTo với query param
3. Clear → navigateTo mà không có query

---

## ▶️ Tiếp Theo

→ [Chapper 5: Middleware](05-middleware.md) - Học về route guards và middleware

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

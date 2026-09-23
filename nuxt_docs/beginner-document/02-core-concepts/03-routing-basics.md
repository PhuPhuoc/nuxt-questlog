# Routing Cơ Bản - File-Based Routing

> **Mục tiêu:** Hiểu cách Nuxt tạo routes từ file structure và cách navigate giữa các trang.

## Mục lục

1. [File-based routing là gì?](#1-file-based-routing-là-gì)
2. [Cơ bản về Routes](#2-cơ-bản-về-routes)
3. [Dynamic Routes](#3-dynamic-routes)
4. [Navigation](#4-navigation)
5. [Route Parameters](#5-route-parameters)
6. [Query Strings và Hash](#6-query-strings-và-hash)

---

## 1. File-Based Routing là gì?

### 1.1 Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FILE-BASED ROUTING                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TRADITIONAL (Vue Router):                                          │
│  ──────────────────────────                                          │
│  router/index.ts:                                                  │
│  const routes = [                                                  │
│    { path: '/', component: Home },                                  │
│    { path: '/about', component: About },                            │
│    { path: '/blog/:slug', component: BlogPost }                    │
│  ]                                                                │
│                                                                     │
│  NUXT (File-based):                                                │
│  ───────────────────                                                │
│  Tạo file = Tạo route! Không cần cấu hình!                        │
│                                                                     │
│  📁 pages/                                                        │
│  ├── 📄 index.vue    →  /                                        │
│  ├── 📄 about.vue   →  /about                                    │
│  └── 📁 blog/                                                     │
│      └── 📄 [slug].vue →  /blog/:slug                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Lợi ích

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LỢI ÍCH CỦA FILE-BASED ROUTING                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Không cần cấu hình router thủ công                           │
│  ✅ Route = File path → Dễ visualize                              │
│  ✅ Tự động tạo navigation                                     │
│  ✅ Lazy loading routes tự động                                  │
│  ✅ Hỗ trợ nested routes                                         │
│  ✅ Dynamic routes đơn giản                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Cơ Bản Về Routes

### 2.1 Route Mapping

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FILE → ROUTE MAPPING                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  File Name                          │  Route URL                      │
│  ────────────────────────────────────────────────────────────────── │
│  pages/index.vue                   │  /                              │
│  pages/about.vue                   │  /about                         │
│  pages/contact.vue                │  /contact                       │
│  pages/blog/index.vue             │  /blog                         │
│  pages/blog/posts.vue             │  /blog/posts                   │
│  pages/user/profile.vue           │  /user/profile                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 Ví dụ: Tạo các trang cơ bản

```vue
<!-- pages/index.vue → URL: / -->
<template>
  <div>
    <h1>Trang Chủ</h1>
  </div>
</template>
```

```vue
<!-- pages/about.vue → URL: /about -->
<template>
  <div>
    <h1>Về Chúng Tôi</h1>
  </div>
</template>
```

```vue
<!-- pages/contact.vue → URL: /contact -->
<template>
  <div>
    <h1>Liên Hệ</h1>
  </div>
</template>
```

### 2.3 Nested Routes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NESTED ROUTES                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  File Structure:                                                   │
│  ───────────────                                                   │
│  pages/                                                            │
│  ├── index.vue           →  /                                    │
│  └── users/                                                         │
│      ├── index.vue      →  /users (parent)                       │
│      ├── profile.vue    →  /users/profile (child)               │
│      └── settings.vue   →  /users/settings (child)              │
│                                                                     │
│  Hoặc dùng parent component:                                       │
│  ──────────────────────────                                        │
│  pages/                                                            │
│  └── users.vue           →  /users (parent wrapper)              │
│      └── (chứa <NuxtPage />)                                      │
│          ├── profile.vue →  /users/profile                       │
│          └── settings.vue →  /users/settings                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Dynamic Routes

### 3.1 Dynamic Segment với []

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DYNAMIC ROUTES                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  [] = Dynamic segment (bắt buộc)                                  │
│  [[]] = Optional segment                                            │
│  [...]] = Catch-all segment                                         │
│                                                                     │
│  EXAMPLES:                                                         │
│  ────────────────────────────────────────────────────────────────── │
│  [id].vue                  →  /:id                                │
│  [category]/[slug].vue   →  /:category/:slug                    │
│  [...slug].vue             →  /:slug+ (catch-all)                │
│  [[slug]].vue             →  / hoặc /:slug                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Dynamic Route với [slug]

```vue
<!-- pages/blog/[slug].vue → URL: /blog/:slug -->
<script setup lang="ts">
// Lấy slug từ URL
const route = useRoute()
const slug = route.params.slug

// Fetch bài viết theo slug
const { data: post } = await useFetch(`/api/posts/${slug}`)
</script>

<template>
  <div>
    <h1>{{ post?.title }}</h1>
    <p>{{ post?.content }}</p>
  </div>
</template>
```

### 3.3 Multiple Dynamic Segments

```vue
<!-- pages/blog/[category]/[year]/[slug].vue -->
<!-- URL: /blog/vue/2024/my-post -->

<script setup lang="ts">
const route = useRoute()

// Các params
const category = route.params.category  // "vue"
const year = route.params.year         // "2024"
const slug = route.params.slug          // "my-post"
</script>
```

### 3.4 Optional Parameters với [[slug]]

```vue
<!-- pages/[[slug]].vue -->
<!-- URL: / hoặc /any-path -->

<script setup lang="ts">
const route = useRoute()

// Optional - có thể undefined
const slug = route.params.slug

if (slug) {
  // Hiển thị theo slug
} else {
  // Hiển thị trang chính
}
</script>
```

### 3.5 Catch-all với [...slug]

```vue
<!-- pages/[...slug].vue -->
<!-- URL: /a hoặc /a/b hoặc /a/b/c (bất kỳ độ sâu nào) -->

<script setup lang="ts">
const route = useRoute()

// slug là array
const slugParts = route.params.slug  // ['a'] hoặc ['a', 'b'] hoặc ['a', 'b', 'c']
const fullPath = slugParts.join('/')
</script>
```

---

## 4. Navigation

### 4.1 NuxtLink - Link Component

```vue
<!-- Thay vì <a href>, dùng <NuxtLink> -->
<template>
  <nav>
    <!-- Link đến trang chủ -->
    <NuxtLink to="/">Trang Chủ</NuxtLink>
    
    <!-- Link đến about -->
    <NuxtLink to="/about">Về Chúng Tôi</NuxtLink>
    
    <!-- Link đến blog với slug -->
    <NuxtLink to="/blog/my-first-post">Bài viết</NuxtLink>
    
    <!-- Link với query string -->
    <NuxtLink to="/blog?category=vue">Blog Vue</NuxtLink>
  </nav>
</template>
```

### 4.2 Programmatic Navigation

```vue
<script setup lang="ts">
const router = useRouter()

// Navigate đến path
router.push('/about')

// Navigate với query
router.push({ path: '/blog', query: { category: 'vue' } })

// Navigate với params
router.push({ name: 'blog-slug', params: { slug: 'my-post' } })

// Navigate với hash
router.push('/about#team')

// Replace (thay thế history entry)
router.replace('/contact')

// Go back/forward
router.back()
router.forward()

// Go by steps
router.go(-1)   // Back
router.go(1)    // Forward
</script>
```

### 4.3 navigateTo Helper

```typescript
// Dùng navigateTo - cú pháp mới hơn
await navigateTo('/about')

// Với options
await navigateTo({
  path: '/blog',
  query: { page: 1 }
})

// Redirect
await navigateTo('/login', { redirectCode: 301 })
```

---

## 5. Route Parameters

### 5.1 useRoute - Lấy Thông Tin Route

```vue
<script setup lang="ts">
const route = useRoute()

// Path hiện tại
console.log(route.path)        // "/blog/my-post"

// Full path với query
console.log(route.fullPath)    // "/blog/my-post?page=1"

// Params (dynamic segments)
console.log(route.params)     // { slug: 'my-post' }

// Query string
console.log(route.query)      // { page: '1' }

// Hash
console.log(route.hash)       // "#section"

// Route name
console.log(route.name)       // "blog-slug"

// Meta data
console.log(route.meta)       // { requiresAuth: true }
</script>
```

### 5.2 Reactive Route

```vue
<script setup lang="ts">
const route = useRoute()

// Route params là reactive - UI update khi URL thay đổi
const slug = computed(() => route.params.slug)

// Watch route changes
watch(() => route.params.slug, (newSlug) => {
  console.log('Slug changed to:', newSlug)
  // Fetch data mới
})
</script>
```

### 5.3 Route Object Types

```typescript
// Định nghĩa kiểu cho route params
interface RouteParams {
  slug: string
}

// Sử dụng trong component
const route = useRoute<RouteParams>()
const slug = route.params.slug // Type: string
```

---

## 6. Query Strings và Hash

### 6.1 Đọc Query Strings

```vue
<script setup lang="ts">
const route = useRoute()

// Query string
const page = route.query.page     // "1" hoặc undefined
const category = route.query.category  // "vue" hoặc undefined

// Chuyển đổi sang number
const pageNum = computed(() => {
  return Number(route.query.page) || 1
})
</script>

<template>
  <div>
    <p>Trang: {{ pageNum }}</p>
    <p v-if="category">Danh mục: {{ category }}</p>
  </div>
</template>
```

### 6.2 Thêm Query String

```vue
<script setup lang="ts">
const router = useRouter()

// Thêm query khi navigate
function goToPage(page: number) {
  router.push({
    path: '/blog',
    query: { page: String(page) }
  })
}

// Giữ nguyên query hiện tại
function nextPage() {
  const currentPage = Number(route.query.page) || 1
  router.push({
    query: { ...route.query, page: String(currentPage + 1) }
  })
}
</script>
```

### 6.3 Hash Navigation

```vue
<template>
  <div>
    <!-- Link đến section cụ thể -->
    <NuxtLink to="/about#team">Team Section</NuxtLink>
    <NuxtLink to="/post#comments">Comments</NuxtLink>
    
    <!-- Scroll đến element -->
    <div id="team">
      <h2>Our Team</h2>
    </div>
  </div>
</template>
```

### 6.4 Đọc Hash

```typescript
const route = useRoute()

// Hash (không có #)
const section = route.hash  // "#team" → "team"
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING CHEAT SHEET                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  FILE → ROUTE:                                                     │
│  ├── index.vue → /                                               │
│  ├── about.vue → /about                                          │
│  └── blog/[slug].vue → /blog/:slug                              │
│                                                                     │
│  DYNAMIC ROUTES:                                                   │
│  ├── [id].vue → /:id                                             │
│  ├── [cat]/[id].vue → /:cat/:id                                 │
│  └── [...path].vue → /* (catch-all)                              │
│                                                                     │
│  NAVIGATION:                                                       │
│  ├── <NuxtLink to="/"> - Link component                           │
│  ├── router.push('/path') - Navigate programmatically             │
│  └── navigateTo('/path') - Async navigation                       │
│                                                                     │
│  ROUTE INFO:                                                      │
│  ├── route.params - Dynamic segments                              │
│  ├── route.query - Query string                                   │
│  ├── route.hash - Hash/anchor                                     │
│  └── route.meta - Route metadata                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [04-routing-advanced.md](04-routing-advanced.md) - Nested Routes & Layouts

hoặc → [05-middleware.md](05-middleware.md) - Middleware & Auth

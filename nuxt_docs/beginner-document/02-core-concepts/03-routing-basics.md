# Routing Cơ Bản

> **Mục tiêu:** Hiểu cách Nuxt tạo routes tự động từ file system trong Nuxt 4.

## Mục lục

1. [Routing là gì?](#1-routing-là-gì)
2. [File-Based Routing](#2-file-based-routing)
3. [NuxtLink](#3-nuxtlink)
4. [Dynamic Routes](#4-dynamic-routes)
5. [useRoute và useRouter](#5-useroute-và-userouter)

---

## 1. Routing là gì?

### 1.1 Dùng để làm gì?

**Routing = Điều hướng giữa các trang trong ứng dụng.**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING - GIẢI THÍCH ĐƠN GIẢN                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  URL                     →         Component/Page                  │
│  ────────────────────────────────────────────────────────────      │
│  /                       →         pages/index.vue                 │
│  /about                 →         pages/about.vue                  │
│  /blog                  →         pages/blog/index.vue             │
│  /blog/vue3             →         pages/blog/[slug].vue          │
│                                                                     │
│  Khi user click link → URL thay đổi → Page thay đổi            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Có Sẵn Hay Cần Custom?

**ROUTING LÀ TÍNH NĂNG CÓ SẴN CỦA NUXT 4!**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT TỰ LÀM HẾT CHO BẠN!                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE ROUTER (Vue thuần):                                         │
│  ──────────────────────────────────────────────────────────────    │
│  1. Cài đặt: npm install vue-router                             │
│  2. Tạo router config: router/index.ts                          │
│  3. Định nghĩa routes                                          │
│  4. Register router trong app                                   │
│                                                                     │
│  NUXT ROUTING (Nuxt 4):                                         │
│  ────────────────────────────────────────────────────────────     │
│  1. KHÔNG cần cài đặt gì!                                      │
│  2. Tạo file trong app/pages/                                   │
│  3. Xong! Routes tự động được tạo                              │
│                                                                     │
│  → FILE PATH = ROUTE PATH                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.3 Cơ Chế Hoạt Động - Behind The Scenes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT ROUTING HOẠT ĐỘNG NHƯ THẾ NÀO?          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. NUXT SCAN THƯ MỤC                                          │
│     Nuxt scan thư mục app/pages/                                 │
│     └── app/pages/index.vue                                      │
│     └── app/pages/blog/index.vue                                 │
│     └── app/pages/blog/[slug].vue                               │
│                                                                     │
│  2. NUXT TẠO ROUTES                                           │
│     Nuxt tự động map file path → route path                     │
│     └── /              → index.vue                              │
│     └── /blog          → blog/index.vue                         │
│     └── /blog/:slug   → blog/[slug].vue                        │
│                                                                     │
│  3. VITE/VUE ROUTER RESOLVE                                     │
│     Khi user navigate → render component tương ứng              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. File-Based Routing

### 2.1 Cấu trúc cơ bản

```
📁 app/pages/
├── 📄 index.vue        → /                  (trang chủ)
├── 📄 about.vue       → /about
├── 📄 contact.vue    → /contact
├── 📄 blog.vue        → /blog
└── 📄 products.vue   → /products
```

### 2.2 Tạo Route đầu tiên

```vue
<!-- app/pages/index.vue → URL: / -->
<script setup lang="ts">
</script>

<template>
  <div>
    <h1>Trang Chủ</h1>
    <NuxtLink to="/about">Giới thiệu</NuxtLink>
  </div>
</template>
```

```vue
<!-- app/pages/about.vue → URL: /about -->
<script setup lang="ts">
</script>

<template>
  <div>
    <h1>Giới Thiệu</h1>
    <NuxtLink to="/">Về trang chủ</NuxtLink>
  </div>
</template>
```

### 2.3 Nested Routes

**Nested routes tổ chức routes theo cấu trúc phân cấp.**

```
📁 app/pages/
├── 📄 index.vue                → /
├── 📄 blog.vue                → /blog (parent layout)
└── 📁 blog/
    ├── 📄 index.vue         → /blog (danh sách bài viết)
    └── 📄 [slug].vue        → /blog/:slug (chi tiết bài viết)
```

**Cách hoạt động:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NESTED ROUTES FLOW                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  URL: /blog                                                       │
│  → Render blog.vue với <NuxtPage /> render blog/index.vue       │
│                                                                     │
│  URL: /blog/my-post                                              │
│  → Render blog.vue với <NuxtPage /> render blog/[slug].vue       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```vue
<!-- app/pages/blog.vue → /blog (parent layout) -->
<script setup lang="ts">
</script>

<template>
  <div class="blog-layout">
    <h1>Blog</h1>
    <!-- Navigation tabs -->
    <nav>
      <NuxtLink to="/blog">Danh sách</NuxtLink>
      <NuxtLink to="/blog/new">Viết bài mới</NuxtLink>
    </nav>

    <!-- Nested page sẽ render ở đây -->
    <NuxtPage />
  </div>
</template>
```

### 2.4 Catch-all Routes

```
📁 app/pages/
├── 📄 index.vue           → /
├── 📄 [...slug].vue      → /* (bất kỳ URL nào)
└── 📄 [[lang]].vue       → / hoặc /en, /vi (optional)
```

```vue
<!-- app/pages/[...slug].vue → Bắt mọi URL -->
<script setup lang="ts">
const route = useRoute()
const slugParts = route.params.slug as string[]  // Array

// /a/b/c → ['a', 'b', 'c']
</script>

<template>
  <div>
    <h1>404 - Trang không tìm thấy</h1>
    <p>Path: {{ slugParts.join(' / ') }}</p>
  </div>
</template>
```

---

## 3. NuxtLink

### 3.1 Dùng để làm gì?

**`<NuxtLink>` = Thay thế cho `<a href="">` trong Nuxt.**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXTLINK KHÁC GÌ <a href="">?                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  <NuxtLink> có những tính năng đặc biệt:                         │
│  ├── Tự động prefetch trang khi link hiện trong viewport       │
│  ├── Tự động thêm class "active" cho link hiện tại             │
│  ├── Hỗ trợ page transitions                                     │
│  └── Không reload toàn bộ page (SPA navigation)                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Có Sẵn Hay Cần Custom?

**`<NuxtLink>` LÀ COMPONENT CÓ SẴN CỦA NUXT 4!**

- Không cần import
- Auto-imported từ Nuxt
- Dùng thay cho `<a>` trong Nuxt

### 3.3 Cách Sử Dụng

```vue
<template>
  <nav>
    <!-- Link cơ bản -->
    <NuxtLink to="/">Trang chủ</NuxtLink>

    <!-- Link với params -->
    <NuxtLink :to="`/blog/${slug}`">Xem bài viết</NuxtLink>

    <!-- Link với query -->
    <NuxtLink to="/search?q=vue">Tìm kiếm</NuxtLink>

    <!-- Link thay thế (replace state) -->
    <NuxtLink to="/new" replace>Thay thế history</NuxtLink>

    <!-- External link -->
    <NuxtLink to="https://google.com" external target="_blank">
      Google
    </NuxtLink>
  </nav>
</template>
```

### 3.4 Active State

```vue
<template>
  <nav>
    <!-- class tự động được thêm -->
    <NuxtLink to="/" class="nav-link">
      Trang chủ
    </NuxtLink>
    <!-- Khi URL = / → class="nav-link router-link-active" -->
    <!-- Khi URL = /about → class="nav-link" -->

    <!-- Exact match -->
    <NuxtLink to="/about" class="nav-link" exact>
      Giới thiệu
    </NuxtLink>
    <!-- Chỉ active khi URL khớp CHÍNH XÁC -->
  </nav>
</template>

<style scoped>
/* Style cho link active */
.nav-link {
  color: gray;
}

.nav-link.router-link-active {
  color: green;
  font-weight: bold;
}

/* Exact active */
.nav-link.router-link-exact-active {
  color: #42b883;
  border-bottom: 2px solid #42b883;
}
</style>
```

### 3.5 Disable Prefetch

```vue
<template>
  <!-- Tắt prefetch cho link nặng -->
  <NuxtLink to="/heavy-page" no-prefetch>
    Trang nặng
  </NuxtLink>
</template>
```

---

## 4. Dynamic Routes

### 4.1 Cấu trúc Dynamic Routes

```
📁 app/pages/
├── 📄 index.vue                  → /
├── 📄 user.vue                  → /user
├── 📄 user/[id].vue            → /user/:id
└── 📄 user/[id]/[tab].vue       → /user/:id/:tab
```

### 4.2 Lấy Params từ URL

```vue
<!-- app/pages/user/[id].vue -->
<script setup lang="ts">
const route = useRoute()

// route.params.id sẽ có giá trị từ URL
// /user/123 → route.params.id = "123"
const userId = route.params.id as string
</script>

<template>
  <div>
    <h1>User ID: {{ userId }}</h1>
  </div>
</template>
```

### 4.3 Dynamic Routes với Data Fetching

```vue
<!-- app/pages/blog/[slug].vue -->
<script setup lang="ts">
const route = useRoute()
const slug = route.params.slug as string

// Fetch bài viết theo slug
const { data: post } = await useFetch(`/api/posts/${slug}`)
</script>

<template>
  <article v-if="post">
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>
  </article>

  <div v-else>
    <h1>Bài viết không tồn tại</h1>
    <NuxtLink to="/blog">Quay lại</NuxtLink>
  </div>
</template>
```

### 4.4 Optional Parameters

```
📁 app/pages/
├── 📄 [[lang]].vue       → / hoặc /en, /vi, /fr
└── 📄 [[lang]]/
    └── 📄 about.vue      → /about hoặc /en/about, /vi/about
```

```vue
<!-- app/pages/[[lang]].vue -->
<script setup lang="ts">
const route = useRoute()

// route.params.lang có thể là undefined, 'en', 'vi', etc.
const lang = (route.params.lang as string) || 'vi'
</script>
```

---

## 5. useRoute và useRouter

### 5.1 useRoute - Lấy thông tin URL

**Dùng để làm gì?** Lấy thông tin về URL hiện tại (params, query, path, etc.)

```vue
<script setup lang="ts">
const route = useRoute()

// Properties của route
console.log(route.path)       // "/blog/vue3-guide"
console.log(route.fullPath)   // "/blog/vue3-guide?tab=comments"
console.log(route.params)     // { slug: "vue3-guide" }
console.log(route.query)      // { tab: "comments" }
console.log(route.name)       // "blog-slug"
console.log(route.meta)       // { title: "Vue 3 Guide" }

// Reactive - cập nhật khi URL thay đổi
watch(() => route.params.slug, (newSlug) => {
  console.log('Slug changed to:', newSlug)
})
</script>
```

### 5.2 useRouter - Điều hướng

**Dùng để làm gì?** Điều hướng đến trang khác (push, replace, back, etc.)

```vue
<script setup lang="ts">
const router = useRouter()

// Navigate đến một URL
function goToHome() {
  router.push('/')
}

// Navigate với params
function goToUser(id: string) {
  router.push(`/user/${id}`)
}

// Navigate với query
function search(query: string) {
  router.push({ path: '/search', query: { q: query } })
}

// Navigate và thay thế (không lưu vào history)
function replacePage() {
  router.replace('/new-page')
}

// Navigate về trang trước
function goBack() {
  router.back()
}

// Navigate đến trang kế tiếp
function goForward() {
  router.forward()
}
</script>
```

### 5.3 navigateTo - Helper function

**Dùng để làm gì?** Navigate đơn giản, có thể dùng trong setup (return được).

```vue
<script setup lang="ts">
// navigateTo là shorthand cho router.push
// Nên dùng vì ngắn gọn hơn

// Đơn giản
await navigateTo('/about')

// Với params
await navigateTo('/user/123')

// Với query
await navigateTo({ path: '/search', query: { q: 'vue' } })

// Replace (không lưu history)
await navigateTo('/new', { replace: true })

// Redirect với return (trong middleware)
return navigateTo('/login')
</script>
```

### 5.4 So Sánh useRoute vs useRouter vs navigateTo

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SO SÁNH: useRoute vs useRouter vs navigateTo     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  useRoute():                                                       │
│  ├── ĐỌC thông tin URL                                            │
│  ├── Lấy params, query, path                                       │
│  └── Ví dụ: route.params.id                                       │
│                                                                     │
│  useRouter():                                                      │
│  ├── ĐIỀU HƯỚNG (navigation)                                      │
│  ├── push(), replace(), back()                                     │
│  └── Ví dụ: router.push('/about')                                  │
│                                                                     │
│  navigateTo():                                                     │
│  ├── ĐIỀU HƯỚNG (simplified)                                     │
│  ├── Dùng được trong setup()                                        │
│  └── Ví dụ: await navigateTo('/about')                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.5 Cases Thực Tế Hay Dùng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING - CASE THỰC TẾ                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CASE 1: REDIRECT SAU ACTION                                     │
│  ───────────────────────────────────────                            │
│  async function handleLogin() {                                     │
│    await $fetch('/api/login', ...)                                 │
│    navigateTo('/dashboard')  // Redirect sau login                  │
│  }                                                                 │
│                                                                     │
│  CASE 2: DYNAMIC NAVIGATION                                       │
│  ───────────────────────────────────────                            │
│  <NuxtLink :to="`/products/${product.id}`">                       │
│  Product {{ product.id }}                                          │
│  </NuxtLink>                                                       │
│                                                                     │
│  CASE 3: QUERY PARAMS                                             │
│  ───────────────────────────────────────                            │
│  router.push({ path: '/search', query: { q: 'vue', page: 1 } })  │
│  // URL: /search?q=vue&page=1                                     │
│                                                                     │
│  CASE 4: MIDDLEWARE REDIRECT                                      │
│  ───────────────────────────────────────                            │
│  if (!isAuth) return navigateTo('/login')                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING CHEAT SHEET                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  FILE → ROUTE:                                                    │
│  ├── app/pages/index.vue       → /                                   │
│  ├── app/pages/about.vue      → /about                              │
│  ├── app/pages/blog/[slug].vue → /blog/:slug                       │
│  └── app/pages/[...all].vue   → /*                                  │
│                                                                     │
│  NAVIGATION:                                                      │
│  ├── <NuxtLink to="/">       → Template link                       │
│  ├── router.push('/path')     → Programmatic navigation             │
│  └── navigateTo('/path')     → Simple redirect (dùng trong setup) │
│                                                                     │
│  ROUTE INFO (useRoute()):                                          │
│  ├── route.params        → Dynamic segments                        │
│  ├── route.query         → Query strings                           │
│  ├── route.path          → Current path                           │
│  └── route.fullPath      → Full path + query                      │
│                                                                     │
│  ACTIVE STATE:                                                    │
│  ├── .router-link-active       → Active prefix                      │
│  └── .router-link-exact-active → Exact match                        │
│                                                                     │
│  LAZY LOADING:                                                    │
│  └── <NuxtLink no-prefetch> → Tắt prefetch                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [04-routing-advanced.md](04-routing-advanced.md) - Nested Routes & Layouts

hoặc → [05-middleware.md](05-middleware.md) - Middleware & Auth

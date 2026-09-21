# Routing Cơ Bản

> **Mục tiêu:** Hiểu cách Nuxt tạo routes tự động từ file system.

## Mục lục

1. [Routing là gì?](#1-routing-là-gì)
2. [File-Based Routing](#2-file-based-routing)
3. [NuxtLink](#3-nuxtlink)
4. [Dynamic Routes](#4-dynamic-routes)
5. [useRoute và useRouter](#5-useroute-và-userouter)

---

## 1. Routing là gì?

### Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING LÀ GÌ?                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Routing = Điều hướng giữa các trang trong ứng dụng           │
│                                                                     │
│  URL                     →         Component/Page                  │
│  ────────────────────────────────────────────────────────────     │
│  /                       →         pages/index.vue                 │
│  /about                 →         pages/about.vue                 │
│  /blog                  →         pages/blog/index.vue            │
│  /blog/vue3             →         pages/blog/[slug].vue         │
│                                                                     │
│  Khi user click link → URL thay đổi → Page thay đổi           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh Vue Router và Nuxt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE ROUTER vs NUXT ROUTING                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE ROUTER (Vue thuần):                                         │
│  ──────────────────────────────────────────────────────────────     │
│  1. Cài đặt: npm install vue-router                             │
│  2. Tạo router config: router/index.js                          │
│  3. Định nghĩa routes:                                         │
│     const routes = [                                             │
│       { path: '/', component: Home },                          │
│       { path: '/about', component: About }                      │
│     ]                                                           │
│  4. Register router trong app                                   │
│                                                                     │
│  NUXT ROUTING:                                                  │
│  ────────────────────────────────────────────────────────────     │
│  1. KHÔNG cần cài đặt gì!                                      │
│  2. Tạo file trong pages/                                       │
│  3. Xong! Routes tự động được tạo                              │
│                                                                     │
│  → FILE PATH = ROUTE PATH                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. File-Based Routing

### 2.1 Cấu trúc cơ bản

```
📁 app/pages/
├── 📄 index.vue        → /                  (trang chủ)
├── 📄 about.vue        → /about
├── 📄 contact.vue     → /contact
├── 📄 blog.vue         → /blog
└── 📄 products.vue    → /products
```

### 2.2 Tạo Route đầu tiên

```vue
<!-- app/pages/index.vue → URL: / -->
<template>
  <div>
    <h1>Trang Chủ</h1>
    <NuxtLink to="/about">Giới thiệu</NuxtLink>
  </div>
</template>
```

```vue
<!-- app/pages/about.vue → URL: /about -->
<template>
  <div>
    <h1>Giới Thiệu</h1>
    <NuxtLink to="/">Về trang chủ</NuxtLink>
  </div>
</template>
```

### 2.3 Nested Routes

Nested routes là cách tổ chức routes theo cấu trúc phân cấp. Khi một route có sub-routes, parent page sẽ chứa `<NuxtPage />` để render nested pages.

```
📁 app/pages/
├── 📄 index.vue                → /
├── 📄 blog.vue                → /blog (parent layout)
└── 📁 blog/
    ├── 📄 index.vue         → /blog (danh sách bài viết)
    └── 📄 [slug].vue        → /blog/:slug (chi tiết bài viết)
```

**Cách hoạt động:**
1. Khi vào `/blog` → render `blog.vue` với `NuxtPage` render `blog/index.vue`
2. Khi vào `/blog/my-post` → render `blog.vue` với `NuxtPage` render `blog/[slug].vue`

```vue
<!-- app/pages/blog.vue → /blog (parent layout) -->
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

**Khi nào dùng Nested Routes:**
- Admin dashboard với sidebar navigation
- Blog với header và sub-pages (danh sách, chi tiết, tạo mới)
- User profile với tabs (thông tin, bài viết, cài đặt)

### 2.4 Catch-all Routes

```
📁 app/pages/
├── 📄 index.vue           → /
├── 📄 [...slug].vue      → /* (bất kỳ URL nào)
└── 📄 [[lang]].vue     → / hoặc /en, /vi (optional)
```

```vue
<!-- app/pages/[...slug].vue → Bắt mọi URL -->
<script setup>
const route = useRoute()
const slugParts = route.params.slug  // Array

console.log(slugParts)
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

### 3.1 NuxtLink là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXTLINK LÀ GÌ?                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  <NuxtLink> = Thay thế cho <a href=""> trong Nuxt              │
│                                                                     │
│  Khác với <a>:                                                    │
│  ├── Tự động prefetch trang khi link hiện trong viewport       │
│  ├── Tự động thêm class "active" cho link hiện tại             │
│  ├── Hỗ trợ page transitions                                     │
│  └── Không reload toàn bộ page (SPA navigation)                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Cách sử dụng

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

### 3.3 Active State

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

### 3.4 Disable Prefetch

```vue
<template>
  <!-- Tắt prefetch cho link nặng -->
  <NuxtLink to="/heavy-page" no-prefetch>
    Trang nặng
  </NuxtLink>

  <!-- Hoặc tắt global prefetch trong nuxt.config.ts -->
</template>
```

---

## 4. Dynamic Routes

### 4.1 Dynamic Segment

```
📁 app/pages/
├── 📄 index.vue                  → /
├── 📄 user.vue                 → /user
├── 📄 user/[id].vue            → /user/:id
└── 📄 user/[id]/[tab].vue    → /user/:id/:tab
```

### 4.2 Lấy Params từ URL

```vue
<!-- app/pages/user/[id].vue -->
<script setup>
// Lấy params từ URL
const route = useRoute()

// route.params.id sẽ có giá trị từ URL
console.log(route.params.id)
// /user/123 → 123
</script>

<template>
  <div>
    <h1>User ID: {{ route.params.id }}</h1>
  </div>
</template>
```

### 4.3 Dynamic Routes với Data

```vue
<!-- app/pages/blog/[slug].vue -->
<script setup>
const route = useRoute()
const slug = route.params.slug

// Mock data - tìm bài viết theo slug
const posts = {
  'vue3-guide': {
    title: 'Hướng dẫn Vue 3',
    content: 'Nội dung bài viết...'
  },
  'nuxt-intro': {
    title: 'Giới thiệu Nuxt',
    content: 'Nội dung bài viết...'
  }
}

const post = computed(() => posts[slug])
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
├── 📄 [[lang]].vue     → / hoặc /en, /vi, /fr
└── 📄 [[lang]]/
    └── 📄 about.vue   → /about hoặc /en/about, /vi/about
```

```vue
<!-- app/pages/[[lang]].vue -->
<script setup>
const route = useRoute()

// route.params.lang có thể là undefined, 'en', 'vi', etc.
const lang = route.params.lang || 'vi'
</script>
```

---

## 5. useRoute và useRouter

### 5.1 useRoute - Lấy thông tin URL

```vue
<script setup>
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

```vue
<script setup>
const router = useRouter()

// Navigate đến một URL
function goToHome() {
  router.push('/')
}

// Navigate với params
function goToUser(id) {
  router.push(`/user/${id}`)
}

// Navigate với query
function search(query) {
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

```vue
<script setup>
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

// Redirect với middleware
return navigateTo('/login')
</script>
```

### 5.4 Ví dụ: Navigation với nhiều cách

```vue
<script setup>
// Cách 1: NuxtLink (trong template)
const to = '/about'
</script>

<template>
  <div>
    <!-- Trong template: dùng NuxtLink -->
    <NuxtLink to="/">Home</NuxtLink>

    <!-- Dynamic link -->
    <NuxtLink :to="`/blog/${slug}`">Read more</NuxtLink>

    <!-- Programmatic navigation -->
    <button @click="router.push('/about')">Go to About</button>

    <!-- Hoặc dùng navigateTo -->
    <button @click="navigateTo('/contact')">Go to Contact</button>
  </div>
</template>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING CHEAT SHEET                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  FILE → ROUTE:                                                    │
│  ├── pages/index.vue       → /                                   │
│  ├── pages/about.vue      → /about                               │
│  ├── pages/blog/[slug].vue → /blog/:slug                        │
│  └── pages/[...all].vue   → /*                                   │
│                                                                     │
│  NAVIGATION:                                                      │
│  ├── <NuxtLink to="/">       → Template link                     │
│  ├── router.push('/path')    → Programmatic                     │
│  └── navigateTo('/path')     → Simple redirect                   │
│                                                                     │
│  ROUTE INFO:                                                      │
│  ├── route.params        → Dynamic segments                      │
│  ├── route.query         → Query strings                        │
│  └── route.path          → Current path                         │
│                                                                     │
│  ACTIVE STATE:                                                    │
│  ├── .router-link-active       → Active prefix                   │
│  └── .router-link-exact-active → Exact match                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [04-routing-advanced.md](04-routing-advanced.md) - Nested Routes & Layouts

hoặc → [05-middleware.md](05-middleware.md) - Middleware & Auth

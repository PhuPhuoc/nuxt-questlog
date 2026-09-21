# Bước 2: Tạo Ứng Dụng Đầu Tiên

> **Mục tiêu:** Sau bài này, bạn sẽ tạo được ứng dụng Nuxt đầu tiên và hiểu cách Nuxt hoạt động.

## Mục lục

1. [Ứng dụng chúng ta sẽ tạo](#1-ứng-dụng-chúng-ta-sẽ-tạo)
2. [Bước 1: Cấu trúc thư mục](#2-bước-1-cấu-trúc-thư-mục)
3. [Bước 2: Tạo app.vue](#3-bước-2-tạo-appvue)
4. [Bước 3: Tạo trang chủ](#4-bước-3-tạo-trang-chủ)
5. [Bước 4: Tạo trang About](#5-bước-4-tạo-trang-about)
6. [Bước 5: Thêm Navigation](#6-bước-5-thêm-navigation)
7. [Bước 6: Tạo trang Blog](#7-bước-6-tạo-trang-blog)
8. [Tổng kết và giải thích](#8-tổng-kết-và-giải-thích)

---

## 1. Ứng Dụng Chúng Ta Sẽ Tạo

### Mục tiêu

Chúng ta sẽ tạo một **blog đơn giản** với:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    BLOGTALK - APP                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  🏠 Trang chủ (/)                                                 │
│     ├── Danh sách bài viết                                         │
│     └── Link đến trang chi tiết                                    │
│                                                                     │
│  📝 Trang About (/about)                                           │
│     └── Thông tin về blog                                          │
│                                                                     │
│  📄 Trang Blog (/blog)                                            │
│     └── Danh sách tất cả bài viết                                  │
│                                                                     │
│  📖 Trang Chi Tiết (/blog/[slug])                                 │
│     └── Nội dung bài viết cụ thể                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Trước Khi Bắt Đầu

Đảm bảo bạn đã:
1. Tạo Nuxt project (`bunx nuxi@latest init my-nuxt-app`)
2. Chạy `bun install`
3. Mở project trong VS Code

---

## 2. Bước 1: Cấu Trúc Thư Mục

### Xóa file mặc định (nếu có)

```bash
# Xóa file mặc định (nếu có)
rm -f app/pages/index.vue
```

### Tạo cấu trúc thư mục

```
📁 my-nuxt-app/
├── 📁 app/
│   ├── 📄 app.vue           ← Sẽ tạo
│   └── 📁 pages/           ← Trang web
│       ├── 📄 index.vue     ← Trang chủ (/)
│       ├── 📄 about.vue     ← Trang about (/about)
│       └── 📁 blog/         ← Blog section
│           ├── 📄 index.vue ← Danh sách (/blog)
│           └── 📄 [slug].vue ← Chi tiết (/blog/:slug)
│
└── ...
```

### Cách tạo trong VS Code

1. Mở folder `app/`
2. Click chuột phải → New Folder → `pages`
3. Click chuột phải vào `pages` → New Folder → `blog`
4. Tạo các file Vue bên trong

---

## 3. Bước 2: Tạo app.vue

### app.vue là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                           app.vue                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  app.vue = ROOT COMPONENT của toàn bộ app                          │
│                                                                     │
│  Nó tương tự như:                                                │
│                                                                     │
│  <body>                                                           │
│    <div id="app">                                                │
│      <NuxtLayout>           ← Bọc content với layout              │
│        <NuxtPage />        ← Hiển thị PAGE hiện tại             │
│      </NuxtLayout>                                                 │
│    </div>                                                         │
│  </body>                                                          │
│                                                                     │
│  Khi URL thay đổi → NuxtPage render page tương ứng              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Tạo app.vue

Tạo file `app/app.vue`:

```vue
<!-- app/app.vue -->
<script setup>
// File này là root component của toàn bộ app
// NuxtLayout bọc pages với layouts
// NuxtPage hiển thị nội dung của route hiện tại
</script>

<template>
  <div>
    <!-- Accessibility - quan trọng cho screen readers -->
    <NuxtRouteAnnouncer />

    <!-- NuxtLayout + NuxtPage là BẮT BUỘC -->
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
```

### Giải thích từng dòng

```vue
<template>
  <!-- 1. Accessibility -->
  <NuxtRouteAnnouncer />
  <!-- Screen readers sẽ thông báo khi route thay đổi -->

  <!-- 2. Layout wrapper -->
  <NuxtLayout>
  <!-- Bọc content với layout (mặc định: layouts/default.vue) -->
  <!-- Chúng ta sẽ tạo layout ở bước sau -->

    <!-- 3. Page content -->
    <NuxtPage />
    <!-- Hiển thị nội dung của PAGE HIỆN TẠI -->
    <!-- Khi URL = / → render pages/index.vue -->
    <!-- Khi URL = /about → render pages/about.vue -->

  </NuxtLayout>
</template>
```

---

## 4. Bước 3: Tạo Trang Chủ

### Trang chủ sẽ hiển thị:

```
┌─────────────────────────────────────────────────────────────────────┐
│  BLOG                                                                     │
│  ─────────────────────────────────────────────────────────────────  │
│  Chào mừng đến với BlogTalk!                                        │
│                                                                     │
│  📝 Bài viết mới nhất:                                             │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🎯 Tại sao nên dùng Nuxt?                                  │   │
│  │ Nuxt là framework mạnh mẽ built on top of Vue...            │   │
│  │ 📅 15/01/2024 | 👁 100 lượt xem                           │   │
│  │ [Đọc thêm →]                                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 🎨 Hướng dẫn Vue 3 Composition API                        │   │
│  │ Composition API là cách viết code Vue 3...                  │   │
│  │ 📅 10/01/2024 | 👁 250 lượt xem                          │   │
│  │ [Đọc thêm →]                                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [Xem tất cả bài viết →]                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Tạo file `app/pages/index.vue`

```vue
<!-- app/pages/index.vue -->
<script setup>
// Đây là trang chủ - URL: /

// Mock data - sau này sẽ fetch từ API
const posts = ref([
  {
    id: 1,
    slug: 'why-nuxt',
    title: 'Tại sao nên dùng Nuxt?',
    excerpt: 'Nuxt là framework mạnh mẽ built on top of Vue.js với nhiều tính năng ưu việt.',
    date: '15/01/2024',
    views: 100
  },
  {
    id: 2,
    slug: 'vue3-composition-api',
    title: 'Hướng dẫn Vue 3 Composition API',
    excerpt: 'Composition API là cách viết code Vue 3 được khuyến nghị. Học cách dùng ref, computed, watch.',
    date: '10/01/2024',
    views: 250
  },
  {
    id: 3,
    slug: 'typescript-with-nuxt',
    title: 'TypeScript với Nuxt cho người mới',
    excerpt: 'Hướng dẫn setup TypeScript trong Nuxt và những lợi ích khi dùng TypeScript.',
    date: '05/01/2024',
    views: 180
  }
])
</script>

<template>
  <div class="home-page">
    <!-- Header -->
    <header class="hero">
      <h1>BlogTalk</h1>
      <p>Chào mừng đến với BlogTalk!</p>
    </header>

    <!-- Bài viết mới nhất -->
    <section class="latest-posts">
      <h2>📝 Bài viết mới nhất</h2>

      <div class="posts-list">
        <article
          v-for="post in posts"
          :key="post.id"
          class="post-card"
        >
          <h3>{{ post.title }}</h3>
          <p>{{ post.excerpt }}</p>

          <div class="post-meta">
            <span>📅 {{ post.date }}</span>
            <span>👁 {{ post.views }} lượt xem</span>
          </div>

          <!-- Link đến trang chi tiết -->
          <NuxtLink :to="`/blog/${post.slug}`" class="read-more">
            Đọc thêm →
          </NuxtLink>
        </article>
      </div>
    </section>

    <!-- Link đến trang blog -->
    <section class="cta">
      <NuxtLink to="/blog" class="btn-primary">
        Xem tất cả bài viết →
      </NuxtLink>
    </section>
  </div>
</template>

<style scoped>
/* Layout */
.home-page {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
}

/* Hero */
.hero {
  text-align: center;
  padding: 3rem 0;
  margin-bottom: 2rem;
}

.hero h1 {
  font-size: 3rem;
  color: #42b883;
  margin-bottom: 0.5rem;
}

.hero p {
  font-size: 1.25rem;
  color: #666;
}

/* Posts */
.latest-posts h2 {
  margin-bottom: 1.5rem;
  font-size: 1.5rem;
}

.posts-list {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.post-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1.5rem;
  transition: box-shadow 0.2s;
}

.post-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.post-card h3 {
  margin-bottom: 0.5rem;
  color: #333;
}

.post-card p {
  color: #666;
  margin-bottom: 1rem;
}

.post-meta {
  display: flex;
  gap: 1rem;
  font-size: 0.875rem;
  color: #999;
  margin-bottom: 1rem;
}

.read-more {
  color: #42b883;
  text-decoration: none;
  font-weight: 500;
}

.read-more:hover {
  text-decoration: underline;
}

/* CTA */
.cta {
  text-align: center;
  margin-top: 3rem;
}

.btn-primary {
  display: inline-block;
  background: #42b883;
  color: white;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  text-decoration: none;
  transition: background 0.2s;
}

.btn-primary:hover {
  background: #359268;
}
</style>
```

---

## 5. Bước 4: Tạo Trang About

### Tạo file `app/pages/about.vue`

```vue
<!-- app/pages/about.vue -->
<script setup>
// Đây là trang About - URL: /about

// SEO: Đặt title và description
useSeoMeta({
  title: 'Về Chúng Tôi - BlogTalk',
  description: 'Tìm hiểu về BlogTalk - blog chia sẻ kiến thức về Vue.js và Nuxt'
})
</script>

<template>
  <div class="about-page">
    <header class="page-header">
      <h1>Về Chúng Tôi</h1>
    </header>

    <article class="about-content">
      <section>
        <h2>BlogTalk là gì?</h2>
        <p>
          BlogTalk là blog cá nhân chia sẻ kiến thức về lập trình web,
          đặc biệt là <strong>Vue.js</strong> và <strong>Nuxt</strong>.
        </p>
        <p>
          Mục tiêu của chúng tôi là tạo ra những bài viết dễ hiểu,
          thực tế, và có thể áp dụng ngay vào project của bạn.
        </p>
      </section>

      <section>
        <h2>Công nghệ sử dụng</h2>
        <ul>
          <li><strong>Nuxt 4</strong> - Framework chính</li>
          <li><strong>Vue 3</strong> - Framework frontend</li>
          <li><strong>TypeScript</strong> - Type safety</li>
          <li><strong>Pinia</strong> - State management</li>
        </ul>
      </section>

      <section>
        <h2>Liên hệ</h2>
        <p>
          Bạn có câu hỏi hoặc muốn đóng góp bài viết?
          Liên hệ với chúng tôi qua email: <a href="mailto:hello@blogtalk.com">hello@blogtalk.com</a>
        </p>
      </section>

      <!-- Link về trang chủ -->
      <div class="back-link">
        <NuxtLink to="/">← Quay về trang chủ</NuxtLink>
      </div>
    </article>
  </div>
</template>

<style scoped>
.about-page {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
}

.page-header {
  margin-bottom: 2rem;
  padding-bottom: 1rem;
  border-bottom: 2px solid #42b883;
}

.page-header h1 {
  font-size: 2.5rem;
  color: #333;
}

.about-content section {
  margin-bottom: 2rem;
}

.about-content h2 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: #42b883;
}

.about-content p {
  line-height: 1.8;
  margin-bottom: 1rem;
  color: #555;
}

.about-content ul {
  list-style: none;
  padding: 0;
}

.about-content li {
  padding: 0.5rem 0;
  border-bottom: 1px solid #eee;
}

.back-link {
  margin-top: 3rem;
}

.back-link a {
  color: #666;
  text-decoration: none;
}

.back-link a:hover {
  color: #42b883;
  text-decoration: underline;
}
</style>
```

---

## 6. Bước 5: Thêm Navigation (Header)

### Tạo Layout với Header

Trước tiên, tạo thư mục `layouts`:

```
📁 app/
├── 📁 layouts/
│   └── 📄 default.vue    ← Sẽ tạo
└── ...
```

### Tạo file `app/layouts/default.vue`

```vue
<!-- app/layouts/default.vue -->
<script setup>
// Navigation links
const navLinks = [
  { label: 'Trang chủ', to: '/' },
  { label: 'Blog', to: '/blog' },
  { label: 'About', to: '/about' }
]
</script>

<template>
  <div class="app-layout">
    <!-- Header với Navigation -->
    <header class="app-header">
      <div class="header-content">
        <!-- Logo -->
        <NuxtLink to="/" class="logo">
          BlogTalk
        </NuxtLink>

        <!-- Navigation Links -->
        <nav class="main-nav">
          <NuxtLink
            v-for="link in navLinks"
            :key="link.to"
            :to="link.to"
            class="nav-link"
          >
            {{ link.label }}
          </NuxtLink>
        </nav>
      </div>
    </header>

    <!-- Main Content -->
    <main class="app-main">
      <!-- slot sẽ được thay bằng page content -->
      <slot />
    </main>

    <!-- Footer -->
    <footer class="app-footer">
      <p>© 2024 BlogTalk. Kiến thức chia sẻ - không giới hạn.</p>
    </footer>
  </div>
</template>

<style scoped>
.app-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* Header */
.app-header {
  background: white;
  border-bottom: 1px solid #eee;
  padding: 1rem 0;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  max-width: 800px;
  margin: 0 auto;
  padding: 0 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  font-size: 1.5rem;
  font-weight: bold;
  color: #42b883;
  text-decoration: none;
}

.main-nav {
  display: flex;
  gap: 1.5rem;
}

.nav-link {
  color: #666;
  text-decoration: none;
  padding: 0.5rem 0;
  transition: color 0.2s;
}

.nav-link:hover {
  color: #42b883;
}

/* Active state - tự động thêm bởi NuxtLink */
.nav-link.router-link-active {
  color: #42b883;
  font-weight: 500;
}

/* Main */
.app-main {
  flex: 1;
}

/* Footer */
.app-footer {
  background: #f5f5f5;
  padding: 2rem;
  text-align: center;
  color: #666;
  margin-top: auto;
}
</style>
```

### Tại sao cần Layout?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAYOUT LÀ GÌ?                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  KHÔNG CÓ LAYOUT:                                                 │
│  ─────────────────                                                  │
│  Trang 1: [Header] [Content A] [Footer]                           │
│  Trang 2: [Header] [Content B] [Footer]                            │
│  → Header/footer lặp lại trong MỌI page                           │
│                                                                     │
│  CÓ LAYOUT:                                                        │
│  ─────────                                                          │
│  layouts/default.vue:                                              │
│    [Header]                                                        │
│    <slot /> ← Thay bằng content của page                           │
│    [Footer]                                                        │
│                                                                     │
│  pages/index.vue → Render vào <slot />                            │
│  pages/about.vue → Render vào <slot />                            │
│  → Header/footer chỉ viết 1 lần!                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 7. Bước 6: Tạo Trang Blog

### Cấu trúc blog pages

```
📁 app/pages/
├── 📄 index.vue        → /
├── 📄 about.vue       → /about
└── 📁 blog/
    ├── 📄 index.vue  → /blog
    └── 📄 [slug].vue → /blog/:slug
```

### Tạo `app/pages/blog/index.vue`

```vue
<!-- app/pages/blog/index.vue -->
<script setup>
// URL: /blog

// Mock data
const allPosts = ref([
  {
    id: 1,
    slug: 'why-nuxt',
    title: 'Tại sao nên dùng Nuxt?',
    excerpt: 'Nuxt là framework mạnh mẽ built on top of Vue.js với nhiều tính năng ưu việt như SSR, auto-imports, file-based routing.',
    category: 'Nuxt',
    date: '15/01/2024',
    views: 100
  },
  {
    id: 2,
    slug: 'vue3-composition-api',
    title: 'Hướng dẫn Vue 3 Composition API',
    excerpt: 'Composition API là cách viết code Vue 3 được khuyến nghị. Học cách dùng ref, computed, watch.',
    category: 'Vue',
    date: '10/01/2024',
    views: 250
  },
  {
    id: 3,
    slug: 'typescript-with-nuxt',
    title: 'TypeScript với Nuxt cho người mới',
    excerpt: 'Hướng dẫn setup TypeScript trong Nuxt và những lợi ích khi dùng TypeScript.',
    category: 'TypeScript',
    date: '05/01/2024',
    views: 180
  },
  {
    id: 4,
    slug: 'pinia-state-management',
    title: 'Pinia - Quản lý State trong Nuxt',
    excerpt: 'Tìm hiểu cách dùng Pinia để quản lý state trong ứng dụng Nuxt.',
    category: 'Nuxt',
    date: '01/01/2024',
    views: 320
  }
])

// SEO
useSeoMeta({
  title: 'Blog - BlogTalk',
  description: 'Tất cả bài viết về Vue.js, Nuxt, và lập trình web'
})
</script>

<template>
  <div class="blog-page">
    <header class="page-header">
      <h1>📝 Blog</h1>
      <p>Tất cả bài viết về Vue.js, Nuxt, và lập trình web</p>
    </header>

    <!-- Danh sách bài viết -->
    <div class="posts-grid">
      <article
        v-for="post in allPosts"
        :key="post.id"
        class="post-card"
      >
        <!-- Category badge -->
        <span class="category-badge">{{ post.category }}</span>

        <!-- Title -->
        <NuxtLink :to="`/blog/${post.slug}`">
          <h2>{{ post.title }}</h2>
        </NuxtLink>

        <!-- Excerpt -->
        <p>{{ post.excerpt }}</p>

        <!-- Meta -->
        <div class="post-meta">
          <span>📅 {{ post.date }}</span>
          <span>👁 {{ post.views }} lượt xem</span>
        </div>

        <!-- Read more -->
        <NuxtLink :to="`/blog/${post.slug}`" class="read-more">
          Đọc bài viết →
        </NuxtLink>
      </article>
    </div>

    <!-- Link về trang chủ -->
    <div class="back-link">
      <NuxtLink to="/">← Quay về trang chủ</NuxtLink>
    </div>
  </div>
</template>

<style scoped>
.blog-page {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
}

.page-header {
  text-align: center;
  margin-bottom: 3rem;
}

.page-header h1 {
  font-size: 2.5rem;
  color: #333;
  margin-bottom: 0.5rem;
}

.page-header p {
  color: #666;
}

/* Grid */
.posts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
  margin-bottom: 3rem;
}

.post-card {
  background: white;
  border: 1px solid #eee;
  border-radius: 8px;
  padding: 1.5rem;
  transition: all 0.2s;
}

.post-card:hover {
  border-color: #42b883;
  box-shadow: 0 4px 12px rgba(66, 184, 131, 0.1);
}

.category-badge {
  display: inline-block;
  background: #e8f5e9;
  color: #42b883;
  padding: 0.25rem 0.75rem;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 500;
  margin-bottom: 0.75rem;
}

.post-card h2 {
  font-size: 1.25rem;
  margin-bottom: 0.75rem;
}

.post-card h2 a {
  color: #333;
  text-decoration: none;
}

.post-card h2 a:hover {
  color: #42b883;
}

.post-card p {
  color: #666;
  line-height: 1.6;
  margin-bottom: 1rem;
}

.post-meta {
  display: flex;
  gap: 1rem;
  font-size: 0.875rem;
  color: #999;
  margin-bottom: 1rem;
}

.read-more {
  color: #42b883;
  text-decoration: none;
  font-weight: 500;
}

.read-more:hover {
  text-decoration: underline;
}

.back-link {
  text-align: center;
}

.back-link a {
  color: #666;
  text-decoration: none;
}

.back-link a:hover {
  color: #42b883;
}
</style>
```

### Tạo `app/pages/blog/[slug].vue`

```vue
<!-- app/pages/blog/[slug].vue -->
<script setup>
// URL: /blog/:slug
// Ví dụ: /blog/why-nuxt

// Lấy slug từ URL
const route = useRoute()
const slug = route.params.slug

// Mock data - tìm bài viết theo slug
const posts = {
  'why-nuxt': {
    title: 'Tại sao nên dùng Nuxt?',
    content: `
      <p>Nuxt là một framework mạnh mẽ xây dựng trên nền tảng Vue.js, cung cấp nhiều tính năng ưu việt:</p>
      
      <h3>1. Server-Side Rendering (SSR)</h3>
      <p>Nuxt hỗ trợ SSR sẵn có, giúp cải thiện SEO và tốc độ tải trang. Thay vì chờ JavaScript load xong mới hiển thị nội dung, server sẽ render HTML trước.</p>
      
      <h3>2. Auto-Imports</h3>
      <p>Không cần import Vue APIs như ref(), computed() mỗi lần sử dụng. Nuxt tự động nhận diện và import cho bạn.</p>
      
      <h3>3. File-Based Routing</h3>
      <p>Tạo route bằng cách tạo file. pages/index.vue = route /, pages/about.vue = route /about. Không cần cấu hình router!</p>
      
      <h3>4. Hybrid Rendering</h3>
      <p>Nuxt hỗ trợ nhiều rendering modes: SSR, SSG, SPA, ISR. Chọn mode phù hợp cho từng route.</p>
    `,
    category: 'Nuxt',
    date: '15/01/2024',
    views: 100,
    author: 'Nam'
  },
  'vue3-composition-api': {
    title: 'Hướng dẫn Vue 3 Composition API',
    content: `
      <p>Composition API là cách viết code Vue 3 được khuyến nghị, thay thế Options API của Vue 2.</p>
      
      <h3>ref() và reactive()</h3>
      <p>Tạo reactive state với ref() cho primitive values và reactive() cho objects.</p>
      
      <h3>computed()</h3>
      <p>Tạo computed properties - tự động cập nhật khi dependencies thay đổi.</p>
      
      <h3>watch() và watchEffect()</h3>
      <p>Theo dõi sự thay đổi của reactive values và chạy side effects.</p>
    `,
    category: 'Vue',
    date: '10/01/2024',
    views: 250,
    author: 'Minh'
  },
  'typescript-with-nuxt': {
    title: 'TypeScript với Nuxt cho người mới',
    content: `
      <p>TypeScript mang lại nhiều lợi ích cho việc phát triển ứng dụng Vue/Nuxt.</p>
      
      <h3>Lợi ích của TypeScript</h3>
      <ul>
        <li>Type safety - phát hiện lỗi sớm</li>
        <li>Autocomplete tốt hơn</li>
        <li>Refactoring dễ dàng hơn</li>
        <li>Documentation tự tạo</li>
      </ul>
    `,
    category: 'TypeScript',
    date: '05/01/2024',
    views: 180,
    author: 'Nam'
  }
}

// Lấy bài viết hiện tại
const post = computed(() => posts[slug as keyof typeof posts])

// SEO - Cập nhật title theo bài viết
useSeoMeta({
  title: () => post.value ? `${post.value.title} - BlogTalk` : 'Không tìm thấy',
  description: () => post.value?.title || ''
})
</script>

<template>
  <div class="post-page">
    <!-- Breadcrumb -->
    <nav class="breadcrumb">
      <NuxtLink to="/">Trang chủ</NuxtLink>
      <span>/</span>
      <NuxtLink to="/blog">Blog</NuxtLink>
      <span>/</span>
      <span>{{ post?.title }}</span>
    </nav>

    <!-- Bài viết tồn tại -->
    <article v-if="post">
      <header class="post-header">
        <span class="category-badge">{{ post.category }}</span>
        <h1>{{ post.title }}</h1>

        <div class="post-meta">
          <span>📅 {{ post.date }}</span>
          <span>👁 {{ post.views }} lượt xem</span>
          <span>✍️ {{ post.author }}</span>
        </div>
      </header>

      <!-- Content -->
      <div class="post-content" v-html="post.content"></div>

      <!-- Back to blog -->
      <div class="back-link">
        <NuxtLink to="/blog">← Quay lại Blog</NuxtLink>
      </div>
    </article>

    <!-- Bài viết không tồn tại -->
    <div v-else class="not-found">
      <h1>404 - Bài viết không tồn tại</h1>
      <p>Bài viết bạn đang tìm kiếm không tồn tại.</p>
      <NuxtLink to="/blog">Quay lại Blog</NuxtLink>
    </div>
  </div>
</template>

<style scoped>
.post-page {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
}

/* Breadcrumb */
.breadcrumb {
  display: flex;
  gap: 0.5rem;
  align-items: center;
  margin-bottom: 2rem;
  font-size: 0.875rem;
  color: #666;
}

.breadcrumb a {
  color: #42b883;
  text-decoration: none;
}

.breadcrumb a:hover {
  text-decoration: underline;
}

/* Header */
.post-header {
  margin-bottom: 2rem;
  padding-bottom: 1rem;
  border-bottom: 2px solid #42b883;
}

.category-badge {
  display: inline-block;
  background: #e8f5e9;
  color: #42b883;
  padding: 0.25rem 0.75rem;
  border-radius: 4px;
  font-size: 0.875rem;
  font-weight: 500;
  margin-bottom: 1rem;
}

.post-header h1 {
  font-size: 2rem;
  color: #333;
  margin-bottom: 1rem;
}

.post-meta {
  display: flex;
  gap: 1.5rem;
  color: #666;
  font-size: 0.875rem;
}

/* Content */
.post-content {
  line-height: 1.8;
  color: #444;
}

.post-content :deep(h3) {
  color: #333;
  margin: 2rem 0 1rem;
}

.post-content :deep(p) {
  margin-bottom: 1rem;
}

.post-content :deep(ul) {
  margin-bottom: 1rem;
  padding-left: 1.5rem;
}

.post-content :deep(li) {
  margin-bottom: 0.5rem;
}

/* Back link */
.back-link {
  margin-top: 3rem;
  padding-top: 2rem;
  border-top: 1px solid #eee;
}

.back-link a {
  color: #666;
  text-decoration: none;
}

.back-link a:hover {
  color: #42b883;
}

/* Not found */
.not-found {
  text-align: center;
  padding: 4rem 0;
}

.not-found h1 {
  color: #d32f2f;
  margin-bottom: 1rem;
}

.not-found p {
  color: #666;
  margin-bottom: 2rem;
}

.not-found a {
  color: #42b883;
  text-decoration: none;
}
</style>
```

---

## 8. Tổng Kết và Giải Thích

### Bạn đã tạo được gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    APP STRUCTURE                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📁 app/                                                           │
│  ├── 📄 app.vue                    ← Root component               │
│  │                                                                  │
│  ├── 📁 layouts/                                                       │
│  │   └── 📄 default.vue           ← Layout chung                │
│  │                                                                  │
│  └── 📁 pages/                                                        │
│      ├── 📄 index.vue              → /                           │
│      ├── 📄 about.vue             → /about                       │
│      └── 📁 blog/                                                      │
│          ├── 📄 index.vue         → /blog                        │
│          └── 📄 [slug].vue        → /blog/:slug                  │
│                                                                     │
│  ROUTES:                                                           │
│  ───────                                                           │
│  /                    → pages/index.vue                          │
│  /about               → pages/about.vue                           │
│  /blog                → pages/blog/index.vue                     │
│  /blog/why-nuxt       → pages/blog/[slug].vue                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Các khái niệm quan trọng đã học

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KEY CONCEPTS                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. app.vue = Root component của app                              │
│  2. NuxtLayout bọc pages với layouts                             │
│  3. NuxtPage render page hiện tại                                │
│  4. File path = Route path                                         │
│  5. [slug].vue = Dynamic route                                    │
│  6. NuxtLink = Link với prefetching                               │
│  7. useRoute() = Lấy thông tin URL                               │
│  8. useSeoMeta() = Cập nhật SEO meta                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Chạy thử app

```bash
# Chạy dev server
bun run dev

# Mở trình duyệt
# http://localhost:3000
```

### Những gì bạn nên thử

```
THỬ THÁCH:
─────────
1. Thêm trang mới: /contact
2. Thêm bài viết mới vào blog
3. Đổi màu sắc, layout
4. Thêm category filter vào blog
5. Thêm sidebar vào layout
```

---

## ✅ Checklist - Đã Hoàn Thành?

```
□ Đã tạo app.vue với NuxtLayout và NuxtPage
□ Đã tạo layouts/default.vue với header và footer
□ Đã tạo pages/index.vue (trang chủ)
□ Đã tạo pages/about.vue
□ Đã tạo pages/blog/index.vue (danh sách bài viết)
□ Đã tạo pages/blog/[slug].vue (chi tiết bài viết)
□ Đã chạy app và xem kết quả
□ Đã hiểu cách Nuxt tạo routes từ file paths
```

---

## ▶️ Tiếp Theo

→ [03-nuxt-structure.md](03-nuxt-structure.md) - Giải thích chi tiết cấu trúc thư mục Nuxt

hoặc → [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Học về Auto-imports

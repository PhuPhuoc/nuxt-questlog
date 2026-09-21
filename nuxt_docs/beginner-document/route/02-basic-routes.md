# Chapper 2: Basic Routes - Các Loại Route Cơ Bản

## Mục lục

1. [Static Routes](#1-static-routes)
2. [Dynamic Routes](#2-dynamic-routes)
3. [Catch-all Routes](#3-catch-all-routes)
4. [Optional Routes](#4-optional-routes)
5. [Multiple Dynamic Segments](#5-multiple-dynamic-segments)

---

## 1. Static Routes

### Cách Tạo

Static routes đơn giản nhất - file name = URL path:

```
📁 app/pages/
├── 📄 index.vue     →  /
├── 📄 about.vue     →  /about
├── 📄 contact.vue  →  /contact
└── 📄 dashboard.vue → /dashboard
```

### Ví Dụ

```vue
<!-- app/pages/about.vue -->
<script setup>
// Không cần import gì - auto-imports!
useSeoMeta({
  title: 'Về Chúng Tôi',
  description: 'Tìm hiểu về câu chuyện của chúng tôi'
})
</script>

<template>
  <div>
    <h1>Về Chúng Tôi</h1>
    <p>Nội dung trang About...</p>
  </div>
</template>
```

### Thư Mục vs File

```
# Cách 1: File trực tiếp
pages/about.vue        →  /about

# Cách 2: File trong thư mục (có thể có nested routes)
pages/about/index.vue  →  /about
```

---

## 2. Dynamic Routes

### Cú Pháp

Dynamic routes dùng **dấu ngoặc vuông** `[param]`:

```
📁 app/pages/
├── 📄 index.vue
└── 📄 user/
    └── 📄 [id].vue   →  /user/:id
```

### Lấy Params Trong Component

```vue
<!-- app/pages/user/[id].vue -->
<script setup>
const route = useRoute()

// Lấy param từ URL
const userId = route.params.id

// Fetch user data
const { data: user } = await useFetch(`/api/users/${userId}`)
</script>

<template>
  <div>
    <h1>User ID: {{ userId }}</h1>
    <div v-if="user">
      <p>Name: {{ user.name }}</p>
      <p>Email: {{ user.email }}</p>
    </div>
  </div>
</template>
```

### URL Mapping

| File | URL | `route.params` |
|------|-----|----------------|
| `[id].vue` | `/user/123` | `{ id: '123' }` |
| `[category].vue` | `/products/shoes` | `{ category: 'shoes' }` |
| `[year]/[month].vue` | `/blog/2024/03` | `{ year: '2024', month: '03' }` |

### Ví Dụ: Blog Posts

```
📁 app/pages/
└── 📄 blog/
    ├── 📄 index.vue      →  /blog
    └── 📄 [slug].vue    →  /blog/:slug
```

```vue
<!-- app/pages/blog/[slug].vue -->
<script setup>
const route = useRoute()
const slug = route.params.slug

const { data: post } = await useFetch(`/api/posts/${slug}`)
</script>

<template>
  <article v-if="post">
    <h1>{{ post.title }}</h1>
    <div v-html="post.content"></div>
  </article>
</template>
```

---

## 3. Catch-all Routes

### Khi Nào Dùng?

- Trang 404 - bắt tất cả URL không khớp
- Documentation - `/docs/...`
- Multi-language fallback

### Cú Pháp: `[...slug].vue`

```
📁 app/pages/
└── 📄 [...slug].vue   →  /*
```

### Ví Dụ: 404 Page

```vue
<!-- app/pages/[...slug].vue -->
<script setup>
const route = useRoute()

// slug là array
// /abc/def → ['abc', 'def']
// /unknown → ['unknown']
const pathSegments = route.params.slug
</script>

<template>
  <div class="not-found">
    <h1>404 - Trang không tồn tại</h1>
    <p>Path: /{{ pathSegments.join('/') }}</p>
    <NuxtLink to="/">Quay về trang chủ</NuxtLink>
  </div>
</template>
```

### Ví Dụ: Documentation

```
📁 app/pages/
├── 📄 docs/
│   └── 📄 [...slug].vue  →  /docs/*
│
# URL Examples:
# /docs/getting-started  → slug = ['getting-started']
# /docs/api/auth/login   → slug = ['api', 'auth', 'login']
```

```vue
<!-- app/pages/docs/[...slug].vue -->
<script setup>
const route = useRoute()
const docPath = route.params.slug.join('/')

const { data: doc } = await useFetch(`/api/docs/${docPath}`)
</script>

<template>
  <div v-if="doc">
    <nav class="doc-nav">
      <NuxtLink to="/docs">Home</NuxtLink>
      <span v-for="(segment, i) in route.params.slug" :key="i">
        / <NuxtLink :to="`/docs/${route.params.slug.slice(0, i + 1).join('/')}`">
          {{ segment }}
        </NuxtLink>
      </span>
    </nav>

    <article>
      <h1>{{ doc.title }}</h1>
      <div v-html="doc.content"></div>
    </article>
  </div>
</template>
```

---

## 4. Optional Routes

### Cú Pháp: `[[param]].vue`

Optional routes cho phép URL có hoặc không có segment:

```
📁 app/pages/
└── 📄 [[lang]]/
    ├── 📄 index.vue  →  / hoặc /en, /vi
    └── 📄 about.vue  →  /about hoặc /en/about, /vi/about
```

### Ví Dụ: Multi-language

```vue
<!-- app/pages/[[lang]]/index.vue -->
<script setup>
const route = useRoute()
const supportedLocales = ['vi', 'en', 'ja']

// Lấy language từ URL hoặc default
const lang = computed(() =>
  (route.params.lang as string) || 'vi'
)

// Redirect nếu locale không hỗ trợ
onMounted(() => {
  if (!supportedLocales.includes(lang.value)) {
    navigateTo('/vi')
  }
})
</script>

<template>
  <div>
    <h1>Trang chủ - Ngôn ngữ: {{ lang.toUpperCase() }}</h1>

    <!-- Language switcher -->
    <nav>
      <NuxtLink to="/vi">Tiếng Việt</NuxtLink>
      <NuxtLink to="/en">English</NuxtLink>
      <NuxtLink to="/ja">日本語</NuxtLink>
    </nav>
  </div>
</template>
```

### URL Mapping

| File | URL | `route.params.lang` |
|------|-----|---------------------|
| `[[lang]]/index.vue` | `/` | `undefined` |
| `[[lang]]/index.vue` | `/en` | `'en'` |
| `[[lang]]/about.vue` | `/vi/about` | `'vi'` |

---

## 5. Multiple Dynamic Segments

### Đặt Tên Dynamic Segments

```typescript
// File: pages/[category]-[id].vue
// URL: /electronics/123

const route = useRoute()
route.params.category  // 'electronics'
route.params.id       // '123'
```

### Ví Dụ: Product Detail

```
📁 app/pages/
└── 📄 product/
    └── 📄 [category]/
        └── 📄 [id].vue  →  /product/:category/:id
```

```vue
<!-- app/pages/product/[category]/[id].vue -->
<script setup>
const route = useRoute()

const category = route.params.category
const productId = route.params.id

const { data: product } = await useFetch(
  `/api/products/${category}/${productId}`
)
</script>

<template>
  <div>
    <nav class="breadcrumb">
      <NuxtLink to="/">Home</NuxtLink>
      <NuxtLink :to="`/category/${category}`">{{ category }}</NuxtLink>
      <span>{{ product?.name }}</span>
    </nav>

    <article v-if="product">
      <h1>{{ product.name }}</h1>
      <p>{{ product.description }}</p>
    </article>
  </div>
</template>
```

### Kết Hợp Multiple Formats

```typescript
// File: pages/article-[category]-[id].vue
// URL: /article-javascript-123
//    : /article-python-456

route.params.category  // 'javascript' hoặc 'python'
route.params.id       // '123' hoặc '456'
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTE TYPES SUMMARY                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  STATIC:     about.vue           →  /about                        │
│                                                                     │
│  DYNAMIC:    [id].vue            →  /:id                          │
│              [category]/[id].vue →  /:category/:id                │
│                                                                     │
│  CATCH-ALL:  [...slug].vue       →  /*                            │
│              route.params.slug = ['a', 'b', 'c']                   │
│                                                                     │
│  OPTIONAL:   [[lang]].vue         →  / hoặc /:lang                │
│                                                                     │
│  HYBRID:     [category]-[id].vue →  /:category-:id               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập

### Bài 1: User Profile Pages

Tạo cấu trúc sau:

```
📁 app/pages/
├── 📄 index.vue
└── 📄 user/
    └── 📄 [id].vue    → /user/:id
```

Hiển thị thông tin user từ mock data.

### Bài 2: Blog Archive

Tạo routes cho blog với cấu trúc:

```
/blog                    → Danh sách bài viết
/blog/:slug              → Chi tiết bài viết
/blog/:year/:month       → Bài viết theo tháng
```

### Bài 3: E-commerce Categories

Tạo routes cho:

```
/products                → Tất cả sản phẩm
/products/:category      → Sản phẩm theo danh mục
/products/:category/:id → Chi tiết sản phẩm
```

---

## ▶️ Tiếp Theo

→ [Chapper 3: Nested Routes](03-nested-routes.md) - Học về routes lồng nhau và layouts

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

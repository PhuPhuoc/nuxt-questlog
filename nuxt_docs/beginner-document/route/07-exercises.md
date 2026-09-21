# Chapper 7: Bài Tập Thực Hành

## Mục lục

1. [Bài Tập 1: User Profile Dashboard](#bài-tập-1-user-profile-dashboard)
2. [Bài Tập 2: E-commerce Product Pages](#bài-tập-2-e-commerce-product-pages)
3. [Bài Tập 3: Auth System Hoàn Chỉnh](#bài-tập-3-auth-system-hoàn-chỉnh)
4. [Bài Tập 4: Documentation Site](#bài-tập-4-documentation-site)

---

## Bài Tập 1: User Profile Dashboard

### Yêu Cầu

Xây dựng user profile với nested routes:

```
/user/[id]                → Profile overview
/user/[id]/posts          → User's posts
/user/[id]/followers      → User's followers
/user/[id]/following      → User's following
/user/[id]/settings       → User settings (protected)
```

### Cấu Trúc Files

```
📁 app/pages/
└── 📁 user/
    └── 📁 [id]/
        ├── 📄 [id].vue          ← Parent (profile overview)
        ├── 📄 [id]/
        │   ├── 📄 index.vue   ← /user/:id (nested)
        │   ├── 📄 posts.vue    ← /user/:id/posts
        │   ├── 📄 followers.vue ← /user/:id/followers
        │   ├── 📄 following.vue ← /user/:id/following
        │   └── 📄 settings.vue  ← /user/:id/settings
```

### Đáp Án

#### Parent Component

```vue
<!-- app/pages/user/[id].vue -->
<script setup>
const route = useRoute()
const userId = route.params.id

const tabs = [
  { label: 'Profile', path: `/user/${userId}` },
  { label: 'Posts', path: `/user/${userId}/posts` },
  { label: 'Followers', path: `/user/${userId}/followers` },
  { label: 'Following', path: `/user/${userId}/following` },
  { label: 'Settings', path: `/user/${userId}/settings` },
]
</script>

<template>
  <div class="user-profile-layout">
    <header class="profile-header">
      <h1>User: {{ userId }}</h1>
      <nav class="profile-tabs">
        <NuxtLink
          v-for="tab in tabs"
          :key="tab.path"
          :to="tab.path"
          class="tab-link"
        >
          {{ tab.label }}
        </NuxtLink>
      </nav>
    </header>

    <main class="profile-content">
      <NuxtPage />
    </main>
  </div>
</template>

<style scoped>
.profile-header {
  padding: 2rem;
  background: #f5f5f5;
  border-radius: 8px;
}

.profile-tabs {
  display: flex;
  gap: 1rem;
  margin-top: 1rem;
}

.tab-link {
  padding: 0.5rem 1rem;
  text-decoration: none;
  color: #333;
  border-radius: 4px;
}

.tab-link.router-link-active {
  background: #42b883;
  color: white;
}
</style>
```

#### Nested Pages

```vue
<!-- app/pages/user/[id]/index.vue -->
<template>
  <div class="profile-overview">
    <h2>Thông tin Profile</h2>
    <p>User ID: {{ $route.params.id }}</p>
  </div>
</template>
```

```vue
<!-- app/pages/user/[id]/posts.vue -->
<script setup>
// Mock posts data
const posts = ref([
  { id: 1, title: 'Bài viết 1', date: '2024-01-01' },
  { id: 2, title: 'Bài viết 2', date: '2024-01-02' },
])
</script>

<template>
  <div class="user-posts">
    <h2>Bài viết của User</h2>
    <ul>
      <li v-for="post in posts" :key="post.id">
        <NuxtLink :to="`/post/${post.id}`">
          {{ post.title }}
        </NuxtLink>
      </li>
    </ul>
  </div>
</template>
```

```vue
<!-- app/pages/user/[id]/followers.vue -->
<template>
  <div class="user-followers">
    <h2>Người theo dõi</h2>
    <p>Danh sách người theo dõi của user {{ $route.params.id }}</p>
  </div>
</template>
```

---

## Bài Tập 2: E-commerce Product Pages

### Yêu Cầu

Xây dựng trang sản phẩm với:

```
/products                    → Danh sách sản phẩm
/products/:category          → Sản phẩm theo danh mục
/products/:category/:id     → Chi tiết sản phẩm
```

### Tính Năng

1. Route validation cho category
2. SEO meta tags động
3. Breadcrumb navigation
4. Related products

### Đáp Án

#### Product List Page

```vue
<!-- app/pages/products/index.vue -->
<script setup>
useSeoMeta({
  title: 'Tất cả sản phẩm',
  description: 'Khám phá các sản phẩm của chúng tôi'
})

const { data: products } = await useFetch('/api/products')
</script>

<template>
  <div class="products-page">
    <h1>Tất cả sản phẩm</h1>
    <div class="categories">
      <NuxtLink to="/products/electronics">Electronics</NuxtLink>
      <NuxtLink to="/products/clothing">Clothing</NuxtLink>
      <NuxtLink to="/products/books">Books</NuxtLink>
    </div>

    <div class="products-grid">
      <div v-for="product in products" :key="product.id" class="product-card">
        <NuxtLink :to="`/products/${product.category}/${product.id}`">
          <h3>{{ product.name }}</h3>
          <p>{{ product.price }}đ</p>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>
```

#### Category Page với Validation

```vue
<!-- app/pages/products/[category].vue -->
<script setup>
const route = useRoute()
const category = route.params.category

// Valid categories
const validCategories = ['electronics', 'clothing', 'books']

// Validate
if (!validCategories.includes(category)) {
  throw createError({
    statusCode: 404,
    message: 'Category not found'
  })
}

useSeoMeta({
  title: () => `${category} - Products`,
  description: () => `Browse our ${category} collection`
})

const { data: products } = await useFetch(
  `/api/products?category=${category}`
)
</script>

<template>
  <div class="category-page">
    <nav class="breadcrumb">
      <NuxtLink to="/products">Products</NuxtLink>
      <span>/</span>
      <span>{{ category }}</span>
    </nav>

    <h1>{{ category }}</h1>

    <div class="products-grid">
      <div v-for="product in products" :key="product.id" class="product-card">
        <NuxtLink :to="`/products/${category}/${product.id}`">
          <h3>{{ product.name }}</h3>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>
```

#### Product Detail Page

```vue
<!-- app/pages/products/[category]/[id].vue -->
<script setup>
const route = useRoute()
const { category, id } = route.params

// Route validation
definePageMeta({
  validate: (route) => {
    const validCategories = ['electronics', 'clothing', 'books']
    if (!validCategories.includes(route.params.category)) {
      return false
    }

    const idRegex = /^\d+$/
    if (!idRegex.test(route.params.id)) {
      return { statusCode: 400, statusMessage: 'Invalid product ID' }
    }

    return true
  }
})

// Fetch data
const { data: product } = await useFetch(
  `/api/products/${category}/${id}`
)

// SEO
useSeoMeta({
  title: () => product.value?.name || 'Product',
  description: () => product.value?.description
})
</script>

<template>
  <div class="product-detail">
    <nav class="breadcrumb">
      <NuxtLink to="/products">Products</NuxtLink>
      <span>/</span>
      <NuxtLink :to="`/products/${category}`">{{ category }}</NuxtLink>
      <span>/</span>
      <span>{{ product?.name }}</span>
    </nav>

    <article v-if="product">
      <h1>{{ product.name }}</h1>
      <p class="price">{{ product.price }}đ</p>
      <p>{{ product.description }}</p>

      <button @click="addToCart">Thêm vào giỏ</button>
    </article>
  </div>
</template>
```

---

## Bài Tập 3: Auth System Hoàn Chỉnh

### Yêu Cầu

Xây dựng hệ thống auth với:

1. Auth middleware (global)
2. Role-based access (user, admin)
3. Guest middleware
4. Protected routes

### Cấu Trúc Files

```
📁 app/
├── 📁 middleware/
│   ├── 📄 auth.ts           ← Auth check
│   ├── 📄 auth.global.ts  ← Global auth
│   ├── 📄 admin.ts          ← Admin only
│   └── 📄 guest.ts          ← Guest only
├── 📁 pages/
│   ├── 📄 index.vue
│   ├── 📄 login.vue         ← Guest only
│   ├── 📄 register.vue       ← Guest only
│   ├── 📄 dashboard.vue      ← Auth only
│   └── 📄 admin/
│       └── 📄 index.vue    ← Admin only
└── 📁 layouts/
    └── 📄 auth.vue          ← Auth pages layout
```

### Đáp Án

#### Auth Store

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
    return response
  }

  async function logout() {
    await $fetch('/api/auth/logout', { method: 'POST' })
    user.value = null
    token.value = null
  }

  return { user, token, isLoggedIn, isAdmin, login, logout }
})
```

#### Global Auth Middleware

```typescript
// app/middleware/auth.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  // Skip for public routes
  const publicRoutes = ['/', '/login', '/register', '/blog']
  if (publicRoutes.includes(to.path)) {
    return
  }

  // Skip for API and static
  if (to.path.startsWith('/api') || to.path.startsWith('/_nuxt')) {
    return
  }

  // Check auth
  if (!authStore.isLoggedIn) {
    return navigateTo({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  }
})
```

#### Admin Middleware

```typescript
// app/middleware/admin.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  if (!authStore.isAdmin) {
    return navigateTo('/403')
  }
})
```

#### Guest Middleware

```typescript
// app/middleware/guest.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()

  if (authStore.isLoggedIn) {
    return navigateTo('/dashboard')
  }
})
```

#### Protected Pages

```vue
<!-- app/pages/dashboard.vue -->
<script setup>
definePageMeta({
  middleware: 'auth'  // Auth check - auto from global
})
</script>

<template>
  <div>
    <h1>Dashboard</h1>
    <p>Chào mừng, {{ useAuthStore().user?.name }}!</p>
  </div>
</template>
```

```vue
<!-- app/pages/admin/index.vue -->
<script setup>
definePageMeta({
  middleware: ['auth', 'admin']
})
</script>

<template>
  <div>
    <h1>Admin Panel</h1>
  </div>
</template>
```

---

## Bài Tập 4: Documentation Site

### Yêu Cầu

Xây dựng documentation site với:

```
/docs                    → Docs home
/docs/[...slug]         → Doc pages
```

### Tính Năng

1. Catch-all routing
2. Sidebar navigation
3. Breadcrumbs
4. Code highlighting

### Đáp Án

#### Docs Layout

```vue
<!-- app/layouts/docs.vue -->
<script setup>
const route = useRoute()

const sidebarLinks = [
  { title: 'Getting Started', items: [
    { label: 'Introduction', path: '/docs/introduction' },
    { label: 'Installation', path: '/docs/installation' },
  ]},
  { title: 'Guides', items: [
    { label: 'Basic Usage', path: '/docs/basic-usage' },
    { label: 'Advanced', path: '/docs/advanced' },
  ]},
]
</script>

<template>
  <div class="docs-layout">
    <aside class="docs-sidebar">
      <NuxtLink to="/docs" class="logo">Documentation</NuxtLink>

      <nav v-for="section in sidebarLinks" :key="section.title">
        <h3>{{ section.title }}</h3>
        <NuxtLink
          v-for="item in section.items"
          :key="item.path"
          :to="item.path"
        >
          {{ item.label }}
        </NuxtLink>
      </nav>
    </aside>

    <main class="docs-content">
      <slot />
    </main>
  </div>
</template>

<style scoped>
.docs-layout {
  display: flex;
  min-height: 100vh;
}

.docs-sidebar {
  width: 250px;
  padding: 2rem;
  border-right: 1px solid #ddd;
}

.docs-content {
  flex: 1;
  padding: 2rem;
  max-width: 800px;
}
</style>
```

#### Catch-all Route

```vue
<!-- app/pages/docs/[...slug].vue -->
<script setup>
const route = useRoute()
const slug = computed(() =>
  Array.isArray(route.params.slug)
    ? route.params.slug.join('/')
    : route.params.slug
)

// Fetch doc
const { data: doc } = await useFetch(`/api/docs/${slug.value}`)

// Breadcrumbs
const breadcrumbs = computed(() => {
  const parts = route.params.slug as string[]
  return parts.map((part, i) => ({
    label: part,
    path: '/docs/' + parts.slice(0, i + 1).join('/')
  }))
})

useSeoMeta({
  title: () => doc.value?.title || 'Documentation'
})
</script>

<template>
  <div class="doc-page">
    <!-- Breadcrumbs -->
    <nav class="breadcrumbs">
      <NuxtLink to="/docs">Docs</NuxtLink>
      <span v-for="crumb in breadcrumbs" :key="crumb.path">
        / <NuxtLink :to="crumb.path">{{ crumb.label }}</NuxtLink>
      </span>
    </nav>

    <article v-if="doc">
      <h1>{{ doc.title }}</h1>
      <div v-html="doc.content"></div>
    </article>

    <div v-else>
      <h1>Documentation</h1>
      <p>Chọn một chủ đề từ sidebar.</p>
    </div>
  </div>
</template>
```

---

## 🎯 Tóm Tắt Bài Tập

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EXERCISES SUMMARY                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  BÀI 1: User Profile Dashboard                                      │
│  ────────────────────────────────────────────────────────────────  │
│  ✅ Nested routes với <NuxtPage>                                 │
│  ✅ Tab navigation                                                 │
│  ✅ Shared layout cho profile pages                                │
│                                                                     │
│  BÀI 2: E-commerce Product Pages                                   │
│  ────────────────────────────────────────────────────────────────  │
│  ✅ Route validation với definePageMeta                          │
│  ✅ Breadcrumb navigation                                         │
│  ✅ Dynamic SEO                                                    │
│                                                                     │
│  BÀI 3: Auth System Hoàn Chỉnh                                    │
│  ────────────────────────────────────────────────────────────────  │
│  ✅ Global auth middleware                                         │
│  ✅ Role-based access (admin)                                      │
│  ✅ Guest middleware                                               │
│  ✅ Protected routes                                               │
│                                                                     │
│  BÀI 4: Documentation Site                                         │
│  ────────────────────────────────────────────────────────────────  │
│  ✅ Catch-all routing                                              │
│  ✅ Custom layout với sidebar                                     │
│  ✅ Breadcrumbs                                                   │
│  ✅ Dynamic content loading                                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Hoàn Thành!

Bạn đã hoàn thành toàn bộ phần Routing!

→ [ Quay về README](README.md) - Xem lại tổng quan

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

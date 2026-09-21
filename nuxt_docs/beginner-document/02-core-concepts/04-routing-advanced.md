# Nested Routes & Layouts

> **Mục tiêu:** Hiểu cách tổ chức app với layouts và nested routes.

## Mục lục

1. [Layouts là gì?](#1-layouts-là-gì)
2. [Tạo Layouts](#2-tạo-layouts)
3. [Nested Routes](#3-nested-routes)
4. [Layout Switching](#4-layout-switching)
5. [Page Transitions](#5-page-transitions)

---

## 1. Layouts Là Gì?

### Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAYOUTS LÀ GÌ?                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Layout = "Khuôn mẫu" chung cho nhiều pages                      │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── Default Layout   → Header, Footer, Sidebar                    │
│  ├── Admin Layout    → Admin Header, Sidebar                       │
│  ├── Auth Layout     → Logo, Không có nav (login/register)        │
│  └── Blog Layout     → Blog-specific header                        │
│                                                                     │
│  LỢI ÍCH:                                                         │
│  ├── Code lặp lại giữa pages                                    │
│  ├── Dễ thay đổi layout chung                                   │
│  └── Tổ chức code tốt hơn                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh có/không có Layouts

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHÔNG CÓ LAYOUTS                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  pages/index.vue:                                                  │
│  ┌─────────────────────────────────────────┐                      │
│  │ [Header]                                │                      │
│  │ [Sidebar]                               │                      │
│  │ [Content]                               │                      │
│  │ [Footer]                                │                      │
│  └─────────────────────────────────────────┘                      │
│                                                                     │
│  pages/about.vue:                                                 │
│  ┌─────────────────────────────────────────┐                      │
│  │ [Header]                                │                      │
│  │ [Sidebar]                               │                      │
│  │ [Content]                               │                      │
│  │ [Footer]                                │                      │
│  └─────────────────────────────────────────┘                      │
│                                                                     │
│  → Header, Sidebar, Footer LẶP LẠI trong MỌI page!            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                    CÓ LAYOUTS                                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  layouts/default.vue:                                              │
│  ┌─────────────────────────────────────────┐                      │
│  │ [Header]                                │                      │
│  │ [Sidebar]                               │                      │
│  │ <slot /> ← Page content here           │                      │
│  │ [Footer]                                │                      │
│  └─────────────────────────────────────────┘                      │
│                                                                     │
│  pages/index.vue → Render vào <slot />                           │
│  pages/about.vue → Render vào <slot />                            │
│                                                                     │
│  → Header, Sidebar, Footer CHỈ VIẾT 1 LẦN!                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Tạo Layouts

### 2.1 Cấu trúc thư mục

```
📁 app/
├── 📄 app.vue
├── 📁 layouts/
│   ├── 📄 default.vue     ← Mặc định - mọi page dùng
│   ├── 📄 auth.vue       ← Auth pages (login, register)
│   └── 📄 admin.vue      ← Admin pages
└── 📁 pages/
    └── ...
```

### 2.2 Default Layout

```vue
<!-- app/layouts/default.vue -->
<script setup>
// Navigation items
const navItems = [
  { label: 'Trang chủ', to: '/' },
  { label: 'Blog', to: '/blog' },
  { label: 'About', to: '/about' },
  { label: 'Liên hệ', to: '/contact' }
]
</script>

<template>
  <div class="app-layout">
    <!-- Header -->
    <header class="header">
      <div class="container">
        <NuxtLink to="/" class="logo">
          MyApp
        </NuxtLink>

        <nav class="nav">
          <NuxtLink
            v-for="item in navItems"
            :key="item.to"
            :to="item.to"
            class="nav-link"
          >
            {{ item.label }}
          </NuxtLink>
        </nav>
      </div>
    </header>

    <!-- Main Content - Pages render vào đây -->
    <main class="main">
      <slot />
    </main>

    <!-- Footer -->
    <footer class="footer">
      <p>© 2024 MyApp. All rights reserved.</p>
    </footer>
  </div>
</template>

<style scoped>
.app-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.header {
  background: #fff;
  border-bottom: 1px solid #eee;
  padding: 1rem 0;
  position: sticky;
  top: 0;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
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

.nav {
  display: flex;
  gap: 1.5rem;
}

.nav-link {
  color: #666;
  text-decoration: none;
}

.nav-link:hover {
  color: #42b883;
}

.nav-link.router-link-active {
  color: #42b883;
  font-weight: 500;
}

.main {
  flex: 1;
  padding: 2rem 0;
}

.footer {
  background: #f5f5f5;
  padding: 2rem;
  text-align: center;
  color: #666;
}
</style>
```

### 2.3 Auth Layout (Không có nav)

```vue
<!-- app/layouts/auth.vue -->
<template>
  <div class="auth-layout">
    <!-- Chỉ có logo và content - không có nav -->
    <div class="auth-container">
      <NuxtLink to="/" class="logo">
        MyApp
      </NuxtLink>

      <!-- Content -->
      <slot />
    </div>
  </div>
</template>

<style scoped>
.auth-layout {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.auth-container {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
}

.logo {
  display: block;
  font-size: 2rem;
  font-weight: bold;
  color: #42b883;
  text-decoration: none;
  text-align: center;
  margin-bottom: 2rem;
}
</style>
```

### 2.4 Admin Layout

```vue
<!-- app/layouts/admin.vue -->
<script setup>
const menuItems = [
  { label: 'Dashboard', to: '/admin', icon: '📊' },
  { label: 'Users', to: '/admin/users', icon: '👥' },
  { label: 'Posts', to: '/admin/posts', icon: '📝' },
  { label: 'Settings', to: '/admin/settings', icon: '⚙️' }
]
</script>

<template>
  <div class="admin-layout">
    <!-- Sidebar -->
    <aside class="sidebar">
      <div class="sidebar-header">
        <NuxtLink to="/" class="logo">
          Admin Panel
        </NuxtLink>
      </div>

      <nav class="sidebar-nav">
        <NuxtLink
          v-for="item in menuItems"
          :key="item.to"
          :to="item.to"
          class="nav-item"
        >
          <span>{{ item.icon }}</span>
          {{ item.label }}
        </NuxtLink>
      </nav>
    </aside>

    <!-- Main content -->
    <div class="admin-main">
      <header class="admin-header">
        <h1>Admin Dashboard</h1>
        <button>Logout</button>
      </header>

      <main class="admin-content">
        <slot />
      </main>
    </div>
  </div>
</template>

<style scoped>
.admin-layout {
  display: flex;
  min-height: 100vh;
}

.sidebar {
  width: 250px;
  background: #1a1a2e;
  color: white;
  position: fixed;
  height: 100vh;
}

.sidebar-header {
  padding: 1.5rem;
  border-bottom: 1px solid #333;
}

.sidebar-nav {
  padding: 1rem 0;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.75rem 1.5rem;
  color: #999;
  text-decoration: none;
  transition: all 0.2s;
}

.nav-item:hover {
  background: #333;
  color: white;
}

.nav-item.router-link-active {
  background: #42b883;
  color: white;
}

.admin-main {
  flex: 1;
  margin-left: 250px;
}

.admin-header {
  background: white;
  padding: 1rem 2rem;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.admin-content {
  padding: 2rem;
}
</style>
```

---

## 3. Nested Routes

### 3.1 Cấu trúc Nested Routes

Nested routes tổ chức routes theo cấu trúc phân cấp. Parent page chứa `<NuxtPage />` để render nested pages.

```
📁 app/pages/
├── 📄 index.vue                     → /
├── 📄 dashboard.vue              → /dashboard
└── 📁 admin/
    ├── 📄 index.vue             → /admin (dashboard)
    ├── 📄 users.vue            → /admin/users
    └── 📄 settings.vue        → /admin/settings
```

### 3.2 Parent Page với NuxtPage

Parent page có `<NuxtPage />` để render nested content. URL phải khớp để nested page được render.

```vue
<!-- app/pages/admin.vue - Parent component -->
<template>
  <div class="admin-container">
    <h1>Admin Section</h1>

    <!-- Navigation cho admin sub-pages -->
    <nav class="admin-nav">
      <NuxtLink to="/admin">Dashboard</NuxtLink>
      <NuxtLink to="/admin/users">Users</NuxtLink>
      <NuxtLink to="/admin/settings">Settings</NuxtLink>
    </nav>

    <!-- Nested page render ở đây -->
    <NuxtPage />
  </div>
</template>
```

### 3.3 Ví dụ: Blog với Nested Routes

```
📁 app/pages/
├── 📄 blog.vue                    → /blog (layout)
└── 📁 blog/
    ├── 📄 index.vue             → /blog (danh sách)
    ├── 📄 new.vue               → /blog/new (tạo mới)
    └── 📄 [slug].vue           → /blog/:slug (chi tiết)
```

```vue
<!-- app/pages/blog.vue -->
<script setup>
// Fetch categories cho navigation
const categories = ref(['Tech', 'Life', 'Travel'])
</script>

<template>
  <div class="blog-page">
    <!-- Blog Header -->
    <header class="blog-header">
      <h1>📝 Blog</h1>
      <p>Chia sẻ kiến thức và kinh nghiệm</p>
    </header>

    <!-- Category Navigation -->
    <nav class="blog-nav">
      <NuxtLink to="/blog" exact>All</NuxtLink>
      <NuxtLink
        v-for="cat in categories"
        :key="cat"
        :to="`/blog?category=${cat}`"
      >
        {{ cat }}
      </NuxtLink>
    </nav>

    <!-- Nested page content -->
    <main class="blog-content">
      <NuxtPage />
    </main>
  </div>
</template>
```

**Sơ đồ hoạt động:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NESTED ROUTES - FLOW                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  URL: /admin/users                                                │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ app/pages/admin.vue (Parent)                                │   │
│  │                                                             │   │
│  │  <h1>Admin Section</h1>                                   │   │
│  │  <nav>Dashboard | Users | Settings</nav>                   │   │
│  │                                                             │   │
│  │  ┌─────────────────────────────────────────────────────┐   │   │
│  │  │ app/pages/admin/users.vue (Nested)                  │   │   │
│  │  │                                                     │   │   │
│  │  │  <h2>User Management</h2>                         │   │   │
│  │  │  [User list here]                                 │   │   │
│  │  │                                                     │   │   │
│  │  └─────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Layout Switching

### 4.1 Sử dụng definePageMeta

```vue
<!-- pages/login.vue -->
<script setup>
// Sử dụng auth layout thay vì default
definePageMeta({
  layout: 'auth'
})
</script>

<template>
  <div class="login">
    <h1>Login</h1>
    <!-- Login form -->
  </div>
</template>
```

```vue
<!-- pages/admin/index.vue -->
<script setup>
// Sử dụng admin layout
definePageMeta({
  layout: 'admin'
})
</script>

<template>
  <div class="dashboard">
    <h1>Dashboard</h1>
    <!-- Dashboard content -->
  </div>
</template>
```

### 4.2 Dynamic Layout

```vue
<!-- pages/[username].vue -->
<script setup>
// Layout thay đổi theo username
const route = useRoute()
const username = route.params.username

// Admin users get admin layout
const layout = computed(() => {
  if (username === 'admin') return 'admin'
  if (isAuthPage.value) return 'auth'
  return 'default'
})

definePageMeta({
  layout: () => layout.value
})
</script>
```

### 4.3 Override Layout in app.vue

```vue
<!-- app.vue - Override default layout globally -->
<script setup>
// Bạn có thể customize default layout ở đây
</script>

<template>
  <NuxtLayout :layout="currentLayout">
    <NuxtPage />
  </NuxtLayout>
</template>
```

---

## 5. Page Transitions

### 5.1 CSS Transitions

```vue
<!-- app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<style>
/* Page transition classes */
.page-enter-active,
.page-leave-active {
  transition: all 0.2s;
}

.page-enter-from {
  opacity: 0;
  transform: translateY(10px);
}

.page-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>
```

### 5.2 Transition với Layout

```vue
<!-- app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage v-slot="{ Component, route }">
      <transition name="fade" mode="out-in">
        <component :is="Component" :key="route.path" />
      </transition>
    </NuxtPage>
  </NuxtLayout>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.15s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

### 5.3 Named Transitions

```vue
<!-- page.vue -->
<script setup>
definePageMeta({
  pageTransition: {
    name: 'slide',
    mode: 'out-in'
  }
})
</script>
```

```vue
<!-- app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<style>
/* Custom slide transition */
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-enter-from {
  transform: translateX(20px);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(-20px);
  opacity: 0;
}
</style>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAYOUTS CHEAT SHEET                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TẠO LAYOUT:                                                      │
│  ├── File: app/layouts/default.vue                               │
│  ├── Dùng: <slot /> cho page content                            │
│  └── Auto-applied cho mọi page                                  │
│                                                                     │
│  CHỌN LAYOUT CHO PAGE:                                          │
│  ├── definePageMeta({ layout: 'auth' })                        │
│  └── definePageMeta({ layout: 'admin' })                       │
│                                                                     │
│  NESTED ROUTES:                                                   │
│  ├── pages/admin.vue → Parent với <NuxtPage />                  │
│  ├── pages/admin/users.vue → Nested page                        │
│  └── URL: /admin/users                                          │
│                                                                     │
│  TRANSITIONS:                                                     │
│  ├── CSS classes: .page-enter-active, etc.                      │
│  └── definePageMeta: { pageTransition: { name: 'fade' } }     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [05-middleware.md](05-middleware.md) - Middleware & Auth Guards

hoặc → [03-routing-basics.md](03-routing-basics.md) - Quay lại Routing Cơ Bản

# Nested Routes & Layouts - Tổ Chức Ứng Dụng

> **Mục tiêu:** Học cách tổ chức routes phức tạp và sử dụng layouts.

## Mục lục

1. [Nested Routes](#1-nested-routes)
2. [Layouts là gì?](#2-layouts-là-gì)
3. [Default Layout](#3-default-layout)
4. [Custom Layouts](#4-custom-layouts)
5. [Dynamic Layouts](#5-dynamic-layouts)
6. [Page Transitions](#6-page-transitions)

---

## 1. Nested Routes

### 1.1 Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NESTED ROUTES                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nested Routes = Routes trong Routes                               │
│                                                                     │
│  Ví dụ: Dashboard có sidebar + content                        │
│  ├── /dashboard      → Dashboard layout                            │
│  │   ├── /dashboard/stats    → Stats page                          │
│  │   ├── /dashboard/settings → Settings page                        │
│  │   └── /dashboard/users    → Users page                          │
│  └── (Tất cả dùng chung dashboard layout)                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Cách 1: File Structure (Nuxt 4)

```
📁 pages/
└── 📁 dashboard/
    ├── 📄 index.vue        → /dashboard (redirect hoặc overview)
    ├── 📄 stats.vue       → /dashboard/stats
    ├── 📄 settings.vue   → /dashboard/settings
    └── 📄 users.vue       → /dashboard/users
```

```vue
<!-- pages/dashboard/index.vue -->
<template>
  <div>
    <h1>Dashboard</h1>
    <p>Chào mừng đến dashboard!</p>
  </div>
</template>
```

```vue
<!-- pages/dashboard/stats.vue -->
<template>
  <div>
    <h1>Statistics</h1>
    <!-- Stats content -->
  </div>
</template>
```

### 1.3 Cách 2: Parent with NuxtPage

Dùng parent component để bọc child routes:

```
📁 pages/
└── 📄 dashboard.vue      → /dashboard (parent layout)
    └── (children routes)
```

```vue
<!-- pages/dashboard.vue -->
<!-- Parent component - bọc tất cả /dashboard/* routes -->
<script setup lang="ts">
// Sidebar có thể define ở đây
const sidebarItems = [
  { label: 'Overview', to: '/dashboard' },
  { label: 'Stats', to: '/dashboard/stats' },
  { label: 'Settings', to: '/dashboard/settings' }
]
</script>

<template>
  <div class="dashboard-layout">
    <!-- Sidebar -->
    <aside class="sidebar">
      <nav>
        <NuxtLink 
          v-for="item in sidebarItems" 
          :key="item.to"
          :to="item.to"
        >
          {{ item.label }}
        </NuxtLink>
      </nav>
    </aside>

    <!-- Main content - Child routes render ở đây -->
    <main class="content">
      <NuxtPage />
    </main>
  </div>
</template>
```

### 1.4 Navigation trong Nested Routes

```vue
<!-- Trong các child pages (/dashboard/stats, etc) -->
<script setup lang="ts">
// Dùng navigateTo hoặc router
function goToParent() {
  // Về dashboard overview
  navigateTo('/dashboard')
}

// Hoặc back
function goBack() {
  router.back()
}
</script>
```

---

## 2. Layouts là gì?

### 2.1 Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAYOUTS LÀ GÌ?                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Layout = BỌC XUNG QUANH PAGE CONTENT                          │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── Header (logo, navigation)                                     │
│  ├── Sidebar (menu)                                                │
│  ├── Footer (copyright, links)                                     │
│  └── <slot /> (nội dung page)                                     │
│                                                                     │
│  Lợi ích:                                                          │
│  ├── Viết header/footer MỘT LẦN                                    │
│  ├── Tất cả pages tự động có header/footer                        │
│  └── Dễ thay đổi layout cho nhiều pages                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 Default Layout

Khi dùng `<NuxtLayout>` trong app.vue, layout mặc định được áp dụng:

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

Layout mặc định = `layouts/default.vue`

---

## 3. Default Layout

### 3.1 Tạo layouts/default.vue

```vue
<!-- app/layouts/default.vue -->
<script setup lang="ts">
const navItems = [
  { label: 'Trang Chủ', to: '/' },
  { label: 'Blog', to: '/blog' },
  { label: 'About', to: '/about' },
  { label: 'Liên Hệ', to: '/contact' }
]
</script>

<template>
  <div class="app-container">
    <!-- Header -->
    <header class="app-header">
      <div class="header-content">
        <NuxtLink to="/" class="logo">
          BlogTalk
        </NuxtLink>

        <nav class="main-nav">
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

    <!-- Main Content - Page render vào đây -->
    <main class="app-main">
      <slot />
    </main>

    <!-- Footer -->
    <footer class="app-footer">
      <p>© 2024 BlogTalk. Kiến thức chia sẻ.</p>
    </footer>
  </div>
</template>

<style scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-header {
  background: white;
  border-bottom: 1px solid #eee;
  padding: 1rem 0;
  position: sticky;
  top: 0;
}

.header-content {
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

.main-nav {
  display: flex;
  gap: 1.5rem;
}

.nav-link {
  color: #666;
  text-decoration: none;
}

.nav-link:hover,
.nav-link.router-link-active {
  color: #42b883;
}

.app-main {
  flex: 1;
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem 1rem;
  width: 100%;
}

.app-footer {
  background: #f5f5f5;
  padding: 2rem;
  text-align: center;
  color: #666;
}
</style>
```

### 3.2 File Structure

```
📁 app/
├── 📄 app.vue
├── 📁 layouts/
│   └── 📄 default.vue    ← Auto-applied
└── 📁 pages/
    ├── 📄 index.vue
    ├── 📄 about.vue
    └── ...
```

---

## 4. Custom Layouts

### 4.1 Tạo Custom Layout

```vue
<!-- app/layouts/auth.vue -->
<script setup lang="ts">
// Auth layout - không có navigation phức tạp
</script>

<template>
  <div class="auth-layout">
    <!-- Simple header for auth pages -->
    <header class="auth-header">
      <NuxtLink to="/" class="logo">
        BlogTalk
      </NuxtLink>
    </header>

    <!-- Auth content -->
    <main class="auth-content">
      <slot />
    </main>

    <!-- Simple footer -->
    <footer class="auth-footer">
      <p>Bạn chưa có tài khoản? <NuxtLink to="/register">Đăng ký</NuxtLink></p>
    </footer>
  </div>
</template>

<style scoped>
.auth-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #f9f9f9;
}

.auth-header {
  position: absolute;
  top: 0;
  padding: 1rem;
}

.auth-content {
  width: 100%;
  max-width: 400px;
  padding: 2rem;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.auth-footer {
  position: absolute;
  bottom: 2rem;
  text-align: center;
  color: #666;
}
</style>
```

### 4.2 Sử dụng Custom Layout

```vue
<!-- pages/login.vue -->
<script setup lang="ts">
// Áp dụng layout 'auth'
definePageMeta({
  layout: 'auth'
})
</script>

<template>
  <div class="login-page">
    <h1>Đăng Nhập</h1>
    <form>
      <!-- Login form -->
    </form>
  </div>
</template>
```

```vue
<!-- pages/register.vue -->
<script setup lang="ts">
// Cũng dùng auth layout
definePageMeta({
  layout: 'auth'
})
</script>

<template>
  <div class="register-page">
    <h1>Đăng Ký</h1>
    <form>
      <!-- Register form -->
    </form>
  </div>
</template>
```

### 4.3 Layout khác cho Admin

```vue
<!-- app/layouts/admin.vue -->
<script setup lang="ts">
const navItems = [
  { label: 'Dashboard', to: '/admin' },
  { label: 'Posts', to: '/admin/posts' },
  { label: 'Users', to: '/admin/users' },
  { label: 'Settings', to: '/admin/settings' }
]
</script>

<template>
  <div class="admin-layout">
    <!-- Admin Sidebar -->
    <aside class="admin-sidebar">
      <div class="sidebar-header">
        <h3>Admin Panel</h3>
      </div>
      
      <nav class="sidebar-nav">
        <NuxtLink 
          v-for="item in navItems" 
          :key="item.to"
          :to="item.to"
          class="nav-item"
        >
          {{ item.label }}
        </NuxtLink>
      </nav>
    </aside>

    <!-- Main content -->
    <div class="admin-main">
      <!-- Admin header -->
      <header class="admin-header">
        <h2>Admin Dashboard</h2>
        <button @click="logout">Đăng xuất</button>
      </header>

      <!-- Page content -->
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

.admin-sidebar {
  width: 250px;
  background: #1a1a2e;
  color: white;
  padding: 1rem;
}

.sidebar-header h3 {
  padding: 1rem;
  border-bottom: 1px solid #333;
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.nav-item {
  color: #aaa;
  text-decoration: none;
  padding: 0.75rem 1rem;
  border-radius: 4px;
}

.nav-item:hover,
.nav-item.router-link-active {
  background: #333;
  color: white;
}

.admin-main {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.admin-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background: white;
  border-bottom: 1px solid #eee;
}

.admin-content {
  flex: 1;
  padding: 2rem;
}
</style>
```

### 4.4 Áp dụng Admin Layout

```vue
<!-- pages/admin/index.vue -->
<script setup lang="ts">
definePageMeta({
  layout: 'admin',
  middleware: 'admin'  // Auth guard
})
</script>

<template>
  <div>
    <h1>Admin Dashboard</h1>
    <!-- Dashboard content -->
  </div>
</template>
```

---

## 5. Dynamic Layouts

### 5.1 Layout dựa trên User Role

```vue
<script setup lang="ts">
// Kiểm tra user role và chọn layout
const { user } = useAuthStore()

// Computed layout
const layout = computed(() => {
  if (!user.value) return 'auth'
  if (user.value.role === 'admin') return 'admin'
  return 'default'
})
</script>

<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>
```

### 5.2 Override Layout trong Page

```vue
<!-- pages/profile.vue -->
<script setup lang="ts">
// Override layout từ page
definePageMeta({
  layout: 'profile'
})
</script>
```

---

## 6. Page Transitions

### 6.1 Transition cơ bản

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <!-- Transition với name mặc định -->
    <NuxtPage />
  </NuxtLayout>
</template>

<style>
/* CSS transition */
.page-enter-active,
.page-leave-active {
  transition: opacity 0.3s;
}

.page-enter-from,
.page-leave-to {
  opacity: 0;
}
</style>
```

### 6.2 Transition với Direction

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage v-slot="{ Component, route }">
      <Transition name="page" mode="out-in">
        <component :is="Component" :key="route.path" />
      </Transition>
    </NuxtPage>
  </NuxtLayout>
</template>

<style>
.page-enter-active,
.page-leave-active {
  transition: all 0.3s;
}

.page-enter-from {
  opacity: 0;
  transform: translateX(20px);
}

.page-leave-to {
  opacity: 0;
  transform: translateX(-20px);
}
</style>
```

### 6.3 Per-Page Transition

```vue
<!-- pages/about.vue -->
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'slide',
    mode: 'out-in'
  }
})
</script>

<style>
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s, opacity 0.3s;
}

.slide-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(-100%);
  opacity: 0;
}
</style>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAYOUTS & NESTED ROUTES CHEAT SHEET                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NESTED ROUTES:                                                   │
│  ├── Folder structure: pages/dashboard/stats.vue                  │
│  ├── Parent component: pages/dashboard.vue                        │
│  └── <NuxtPage /> trong parent để render children               │
│                                                                     │
│  LAYOUTS:                                                         │
│  ├── layouts/default.vue - Auto-applied                          │
│  ├── layouts/auth.vue - Custom layout                            │
│  ├── layouts/admin.vue - Admin layout                           │
│  └── <NuxtLayout :name="layout"> - Dynamic layout              │
│                                                                     │
│  PAGE-SPECIFIC LAYOUT:                                            │
│  ├── definePageMeta({ layout: 'auth' })                         │
│  └── definePageMeta({ layout: false }) - No layout              │
│                                                                     │
│  TRANSITIONS:                                                      │
│  ├── <NuxtPage v-slot="{ Component }">                         │
│  └── <Transition name="page">                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [05-middleware.md](05-middleware.md) - Middleware & Auth

hoặc → [06-data-fetching.md](06-data-fetching.md) - Data Fetching

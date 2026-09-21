# Chapper 3: Nested Routes & Layouts

## Mục lục

1. [Nested Routes là gì?](#1-nested-routes-là-gì)
2. [Cách Tạo Nested Routes](#2-cách-tạo-nested-routes)
3. [Layouts](#3-layouts)
4. [Dynamic Layouts](#4-dynamic-layouts)

---

## 1. Nested Routes là gì?

### Khái Niệm

Nested routes cho phép render routes con bên trong routes cha thông qua `<NuxtPage>`.

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NESTED ROUTES CONCEPT                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  URL: /user/123/settings                                           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  pages/user/[id].vue (PARENT)                             │   │
│  │                                                          │   │
│  │  ┌─────────────────────────────────────────────────┐    │   │
│  │  │  <NuxtPage />                                  │    │   │
│  │  │  (Renders nested content here)                │    │   │
│  │  └─────────────────────────────────────────────────┘    │   │
│  │                                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  Nested page: pages/user/[id]/settings.vue                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Ví Dụ Thực Tế

```
/user/123             → User profile page
/user/123/posts       → User's posts (nested)
/user/123/settings   → User settings (nested)
/user/123/followers  → User's followers (nested)
```

---

## 2. Cách Tạo Nested Routes

### Cấu Trúc File

```
📁 app/pages/
└── 📁 user/
    ├── 📄 [id].vue              ← Parent route
    ├── 📄 [id]/
    │   ├── 📄 index.vue        ← /user/:id (nested index)
    │   ├── 📄 posts.vue        ← /user/:id/posts
    │   └── 📄 settings.vue     ← /user/:id/settings
    └── 📄 followers.vue         ← /user/:id/followers (sibling)
```

### Parent Component với `<NuxtPage>`

```vue
<!-- app/pages/user/[id].vue -->
<script setup>
const route = useRoute()
const userId = route.params.id
</script>

<template>
  <div class="user-layout">
    <!-- User header - luôn hiển thị -->
    <header class="user-header">
      <h1>User Profile: {{ userId }}</h1>
      <nav class="user-nav">
        <NuxtLink :to="`/user/${userId}`">Profile</NuxtLink>
        <NuxtLink :to="`/user/${userId}/posts`">Posts</NuxtLink>
        <NuxtLink :to="`/user/${userId}/settings`">Settings</NuxtLink>
        <NuxtLink :to="`/user/${userId}/followers`">Followers</NuxtLink>
      </nav>
    </header>

    <!-- Nested page được render ở đây -->
    <main class="user-content">
      <NuxtPage />
    </main>
  </div>
</template>

<style scoped>
.user-layout {
  max-width: 1200px;
  margin: 0 auto;
}

.user-header {
  padding: 2rem;
  background: #f5f5f5;
  border-radius: 8px;
  margin-bottom: 2rem;
}

.user-nav {
  display: flex;
  gap: 1rem;
  margin-top: 1rem;
}

.user-nav a {
  padding: 0.5rem 1rem;
  background: white;
  border-radius: 4px;
  text-decoration: none;
  color: #333;
}

.user-nav a.router-link-active {
  background: #42b883;
  color: white;
}
</style>
```

### Nested Index Page

```vue
<!-- app/pages/user/[id]/index.vue -->
<template>
  <div class="user-profile">
    <h2>Thông tin User</h2>
    <p>User ID: {{ $route.params.id }}</p>
    <p>Đây là trang profile mặc định của user</p>
  </div>
</template>
```

### Nested Pages

```vue
<!-- app/pages/user/[id]/posts.vue -->
<template>
  <div class="user-posts">
    <h2>Bài viết của User</h2>
    <p>User ID: {{ $route.params.id }}</p>
    <!-- Danh sách posts -->
  </div>
</template>
```

```vue
<!-- app/pages/user/[id]/settings.vue -->
<template>
  <div class="user-settings">
    <h2>Cài đặt User</h2>
    <p>User ID: {{ $route.params.id }}</p>
    <!-- Form settings -->
  </div>
</template>
```

### Sibling Routes (Cùng cấp với Parent)

```vue
<!-- app/pages/user/[id]/followers.vue -->
<!-- URL: /user/:id/followers - KHÔNG nested trong [id].vue -->

<template>
  <div class="user-followers">
    <h2>Người theo dõi</h2>
    <p>User ID: {{ $route.params.id }}</p>
  </div>
</template>
```

> **Lưu ý:** `followers.vue` nằm cùng cấp với `[id].vue`, không nested.

---

## 3. Layouts

### Layout Là Gì?

Layout là giao diện chung bao bọc tất cả pages:

```
┌─────────────────────────────────────────────────────────────────────┐
│                         LAYOUT                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  HEADER - Logo, Navigation, Auth                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                          │   │
│  │                   <NuxtPage />                            │   │
│  │               (Page content thay đổi)                    │   │
│  │                                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  FOOTER - Links, Copyright                                │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Tạo Layout

```
📁 app/
├── 📁 layouts/
│   ├── 📄 default.vue      ← Layout mặc định
│   └── 📄 admin.vue       ← Layout cho admin
├── 📁 pages/
│   └── ...
└── 📄 app.vue
```

### Default Layout

```vue
<!-- app/layouts/default.vue -->
<script setup>
const isMenuOpen = ref(false)
</script>

<template>
  <div class="app-layout">
    <!-- Header -->
    <header class="app-header">
      <div class="header-content">
        <NuxtLink to="/" class="logo">MyBlog</NuxtLink>

        <nav class="main-nav" :class="{ 'is-open': isMenuOpen }">
          <NuxtLink to="/" @click="isMenuOpen = false">Home</NuxtLink>
          <NuxtLink to="/blog" @click="isMenuOpen = false">Blog</NuxtLink>
          <NuxtLink to="/about" @click="isMenuOpen = false">About</NuxtLink>
        </nav>

        <button @click="isMenuOpen = !isMenuOpen" class="menu-toggle">
          ☰
        </button>
      </div>
    </header>

    <!-- Main Content -->
    <main class="app-main">
      <slot />  <!-- Nơi pages được render -->
    </main>

    <!-- Footer -->
    <footer class="app-footer">
      <p>© 2024 MyBlog</p>
    </footer>
  </div>
</template>
```

### Sử Dụng Layout Trong app.vue

```vue
<!-- app/app.vue -->
<script setup>
</script>

<template>
  <div>
    <NuxtRouteAnnouncer />
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
```

### Đổi Layout Theo Page

```vue
<!-- app/pages/admin.vue -->
<script setup>
// Chỉ định layout 'admin' thay vì 'default'
definePageMeta({
  layout: 'admin'
})
</script>

<template>
  <div>
    <h1>Admin Dashboard</h1>
  </div>
</template>
```

### Admin Layout

```vue
<!-- app/layouts/admin.vue -->
<script setup>
const authStore = useAuthStore()
</script>

<template>
  <div class="admin-layout">
    <!-- Sidebar -->
    <aside class="admin-sidebar">
      <h2>Admin Panel</h2>
      <nav>
        <NuxtLink to="/admin">Dashboard</NuxtLink>
        <NuxtLink to="/admin/users">Users</NuxtLink>
        <NuxtLink to="/admin/products">Products</NuxtLink>
        <NuxtLink to="/admin/settings">Settings</NuxtLink>
      </nav>

      <div class="sidebar-footer">
        <span>{{ authStore.user?.email }}</span>
        <NuxtLink to="/">← Back to Site</NuxtLink>
      </div>
    </aside>

    <!-- Main Content -->
    <main class="admin-main">
      <slot />
    </main>
  </div>
</template>

<style scoped>
.admin-layout {
  display: flex;
  min-height: 100vh;
}

.admin-sidebar {
  width: 250px;
  background: #1a1a1a;
  color: white;
  padding: 1rem;
}

.admin-sidebar nav {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-top: 1rem;
}

.admin-sidebar a {
  color: white;
  text-decoration: none;
  padding: 0.75rem;
  border-radius: 4px;
}

.admin-sidebar a:hover,
.admin-sidebar a.router-link-active {
  background: #333;
}

.admin-main {
  flex: 1;
  padding: 2rem;
}
</style>
```

---

## 4. Dynamic Layouts

### Chọn Layout Động

```vue
<!-- app/pages/dashboard.vue -->
<script setup lang="ts">
// Layout phụ thuộc vào user role
const { user } = useAuth()

const layout = computed(() => {
  if (!user.value) return 'auth'
  return user.value.role === 'admin' ? 'admin' : 'default'
})

definePageMeta({
  layout: false  // Tắt auto layout
})
</script>

<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>
```

### Auth Layout (Không Header/Footer)

```vue
<!-- app/layouts/auth.vue -->
<template>
  <div class="auth-layout">
    <div class="auth-container">
      <NuxtLink to="/" class="back-link">← Quay về trang chủ</NuxtLink>
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
  padding: 3rem;
  border-radius: 16px;
  width: 100%;
  max-width: 400px;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.25);
}

.back-link {
  display: block;
  margin-bottom: 1.5rem;
  color: #666;
  text-decoration: none;
}
</style>
```

```vue
<!-- app/pages/login.vue -->
<script setup>
definePageMeta({
  layout: 'auth'
})
</script>

<template>
  <div>
    <h1>Đăng nhập</h1>
    <form>
      <!-- Login form -->
    </form>
  </div>
</template>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NESTED ROUTES & LAYOUTS                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NESTED ROUTES:                                                    │
│  ───────────────                                                   │
│  parent/[id].vue              ← Parent với <NuxtPage />           │
│  parent/[id]/child.vue        ← Nested route                      │
│  parent/[id].vue             ← Sibling (cùng cấp)                 │
│                                                                     │
│  LAYOUTS:                                                          │
│  ────────                                                          │
│  layouts/default.vue           ← Mặc định                         │
│  layouts/admin.vue            ← Admin layout                      │
│  layouts/auth.vue            ← Auth layout (login, register)     │
│                                                                     │
│  SWITCHING:                                                        │
│  ──────────                                                        │
│  definePageMeta({ layout: 'admin' })                              │
│  <NuxtLayout :name="layout">                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập

### Bài 1: User Dashboard với Nested Routes

Tạo cấu trúc:

```
pages/
└── user/
    └── [id]/
        ├── index.vue      → /user/:id (profile)
        ├── posts.vue     → /user/:id/posts
        ├── settings.vue  → /user/:id/settings
        └── [id].vue     ← Parent với navigation tabs
```

Parent page hiển thị navigation tabs, nested pages hiển thị nội dung.

### Bài 2: Multi-layout Blog

Tạo:

1. `layouts/blog.vue` - Layout với sidebar cho blog pages
2. `pages/blog/index.vue` - Sử dụng blog layout
3. `pages/blog/[slug].vue` - Sử dụng blog layout

---

## ▶️ Tiếp Theo

→ [Chapper 4: Navigation](04-navigation.md) - Học về NuxtLink, navigateTo, useRouter

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

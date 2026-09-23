# Nested Routes - Routes Lồng Nhau

> **Mục tiêu:** Tạo routes có cấu trúc nested.

## Nested Routes

```
pages/
├── users.vue          → /users (parent)
│   (contains <NuxtPage />)
└── users/
    ├── profile.vue    → /users/profile
    └── settings.vue   → /users/settings
```

## Parent Component

```vue
<!-- pages/users.vue -->
<template>
  <div class="users-layout">
    <aside>Sidebar</aside>
    <main>
      <NuxtPage /> <!-- Child routes render ở đây -->
    </main>
  </div>
</template>
```

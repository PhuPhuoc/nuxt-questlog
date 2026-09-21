# Bước 3: Cấu Trúc Thư Mục Nuxt Chi Tiết

> **Mục tiêu:** Hiểu rõ mỗi thư mục/file trong Nuxt làm gì và khi nào sử dụng.

## Mục lục

1. [Tổng quan cấu trúc](#1-tổng-quan-cấu-trúc)
2. [Thư mục app/](#2-thư-mục-app)
3. [Thư mục server/](#3-thư-mục-server)
4. [Các file cấu hình](#4-các-file-cấu-hình)
5. [Thư mục public/](#5-thư-mục-public)
6. [Quy tắc đặt tên](#6-quy-tắc-đặt-tên)

---

## 1. Tổng Quan Cấu Trúc

### So sánh Nuxt 3 và Nuxt 4

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT 3 vs NUXT 4                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NUXT 3 (OLD)                                                       │
│  ───────────────                                                     │
│  📁 root/                                                           │
│  ├── 📁 pages/          ← Root level                             │
│  ├── 📁 components/                                                 │
│  ├── 📁 composables/                                               │
│  ├── 📁 layouts/                                                   │
│  └── ...                                                            │
│                                                                     │
│  NUXT 4 (NEW)                                                       │
│  ───────────────                                                     │
│  📁 root/                                                           │
│  └── 📁 app/            ← Tất cả trong app/                    │
│      ├── 📁 pages/                                               │
│      ├── 📁 components/                                           │
│      ├── 📁 composables/                                          │
│      ├── 📁 layouts/                                              │
│      └── ...                                                        │
│                                                                     │
│  📁 server/              ← Server code (vẫn ở root)            │
│  📁 public/               ← Static files (vẫn ở root)            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Cấu trúc đầy đủ của Nuxt 4

```
📁 my-nuxt-app/
│
├── 📁 app/                        # ← CODE ỨNG DỤNG
│   ├── 📄 app.vue               # Root component
│   │
│   ├── 📁 pages/               # File-based routing
│   │   ├── 📄 index.vue        # → /
│   │   ├── 📄 about.vue        # → /about
│   │   └── 📁 blog/            # → /blog
│   │       ├── 📄 index.vue    # → /blog
│   │       └── 📄 [slug].vue   # → /blog/:slug
│   │
│   ├── 📁 components/          # Reusable components
│   │   ├── 📄 Header.vue      # → <Header>
│   │   └── 📄 Footer.vue      # → <Footer>
│   │
│   ├── 📁 composables/         # Reusable logic
│   │   └── 📄 useAuth.ts      # → useAuth()
│   │
│   ├── 📁 stores/              # Pinia stores
│   │   └── 📄 auth.ts         # → useAuthStore()
│   │
│   ├── 📁 layouts/             # Page layouts
│   │   ├── 📄 default.vue     # Default layout
│   │   └── 📄 admin.vue       # Admin layout
│   │
│   ├── 📁 middleware/          # Route middleware
│   │   └── 📄 auth.ts        # Auth guard
│   │
│   ├── 📁 plugins/            # App plugins
│   │   └── 📄 analytics.ts   # Run on app start
│   │
│   ├── 📁 utils/              # Utility functions
│   │   └── 📄 formatDate.ts  # → formatDate()
│   │
│   └── 📁 assets/             # Static assets
│       └── 📁 css/           # CSS files
│
├── 📁 server/                   # ← SERVER CODE
│   ├── 📁 api/                # API endpoints
│   │   ├── 📄 users.get.ts    # GET /api/users
│   │   └── 📄 users.post.ts   # POST /api/users
│   ├── 📁 middleware/         # Server middleware
│   └── 📁 utils/              # Server utilities
│
├── 📁 public/                   # ← STATIC FILES
│   ├── 📄 favicon.ico
│   └── 📁 images/
│
├── 📄 nuxt.config.ts          # Nuxt configuration
├── 📄 app.config.ts           # App-specific config
├── 📄 package.json
└── 📄 tsconfig.json
```

---

## 2. Thư Mục app/

### 2.1 app.vue - Root Component

```
┌─────────────────────────────────────────────────────────────────────┐
│                           app.vue                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  app.vue = ROOT COMPONENT của toàn bộ app                          │
│                                                                     │
│  Nó được mount vào <div id="app"> trong HTML                      │
│                                                                     │
│  THƯỜNG CHỨA:                                                     │
│  ├── NuxtRouteAnnouncer (accessibility)                           │
│  ├── NuxtLayout (layout wrapper)                                  │
│  └── NuxtPage (page content)                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```vue
<!-- app.vue -->
<script setup>
// Code ở đây chạy trước mọi page
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

### 2.2 pages/ - File-Based Routing

```
pages/ STRUCTURE:
─────────────────

pages/
├── index.vue              → /
├── about.vue            → /about
├── contact.vue          → /contact
│
├── user/
│   ├── index.vue        → /user
│   └── [id].vue        → /user/:id
│
├── blog/
│   ├── index.vue        → /blog
│   ├── [slug].vue       → /blog/:slug
│   └── [year]/
│       └── [month].vue  → /blog/:year/:month
│
└── [...slug].vue         → /* (catch-all)
```

**Quy tắc:**
- File name = Route path
- `[param].vue` = Dynamic segment
- `[...slug].vue` = Catch-all (bắt mọi thứ)
- `[[param]].vue` = Optional segment

### 2.3 components/ - Reusable Components

```
components/ STRUCTURE:
──────────────────────

components/
├── 📄 Header.vue         → <Header>
├── 📄 Footer.vue        → <Footer>
├── 📄 Button.vue        → <Button>
│
├── 📁 ui/               # Prefix = Ui
│   ├── 📄 Modal.vue     → <UiModal>
│   └── 📄 Toast.vue     → <UiToast>
│
└── 📁 base/             # Prefix = Base
    ├── 📄 Input.vue     → <BaseInput>
    └── 📁 Button.vue    → <BaseButton>
```

**Quy tắc đặt tên:**
- Tên file = Tên component (PascalCase)
- Thư mục con = Prefix
- Lazy loading: `<LazyComponentName>`

### 2.4 composables/ - Reusable Logic

```
composables/ STRUCTURE:
───────────────────────

composables/
├── 📄 useAuth.ts        → useAuth()
├── 📄 useTheme.ts       → useTheme()
└── 📄 useFetchData.ts   → useFetchData()
```

**Quy tắc:**
- Tên file phải bắt đầu bằng `use`
- Export một function/composable
- Auto-imported trong toàn bộ app

### 2.5 stores/ - Pinia State

```
stores/ STRUCTURE:
───────────────────

stores/
├── 📄 auth.ts           → useAuthStore()
├── 📄 cart.ts          → useCartStore()
└── 📄 product.ts       → useProductStore()
```

**Quy tắc đặt tên:**
- Tên function phải kết thúc bằng `Store`
- VD: `useAuthStore`, `useCartStore`

### 2.6 layouts/ - Page Layouts

```
layouts/ STRUCTURE:
───────────────────

layouts/
├── 📄 default.vue      # Default layout (mọi page dùng)
├── 📄 auth.vue        # Auth layout (login, register)
└── 📄 admin.vue       # Admin layout (dashboard)
```

**Sử dụng:**
```vue
<!-- Trong page -->
<script setup>
definePageMeta({
  layout: 'admin'  // Dùng admin layout
})
</script>
```

### 2.7 middleware/ - Route Guards

```
middleware/ STRUCTURE:
──────────────────────

middleware/
├── 📄 auth.ts           # Named middleware
├── 📄 auth.global.ts  # Global middleware (chạy mọi route)
└── 📄 admin.ts         # Admin-only middleware
```

**Loại middleware:**
- `.global.ts` = Chạy trên mọi route
- Không có `.global` = Chỉ chạy khi được apply

### 2.8 plugins/ - App Plugins

```
plugins/ STRUCTURE:
────────────────────

plugins/
├── 📄 analytics.ts       # Client + Server
├── 📄 analytics.client.ts  # Chỉ Client
└── 📄 analytics.server.ts  # Chỉ Server
```

**Plugins chạy:**
- Khi app khởi tạo
- Trước khi page được render

### 2.9 utils/ - Utility Functions

```
utils/ STRUCTURE:
─────────────────

utils/
├── 📄 formatDate.ts     → formatDate()
├── 📄 validateEmail.ts  → validateEmail()
└── 📄 constants.ts      → exported constants
```

**Quy tắc:**
- Auto-imported không cần import
- Nên export function

---

## 3. Thư Mục server/

### 3.1 server/api/ - API Endpoints

```
server/api/ STRUCTURE:
───────────────────────

server/api/
├── 📄 users.get.ts      # GET /api/users
├── 📄 users.post.ts     # POST /api/users
├── 📄 users/[id].get.ts # GET /api/users/:id
└── 📄 users/[id].put.ts # PUT /api/users/:id
```

**Quy tắc đặt tên:**
- File name = HTTP method + path
- `.get.ts` = GET
- `.post.ts` = POST
- `.put.ts` = PUT
- `.delete.ts` = DELETE

### 3.2 server/middleware/ - Server Middleware

```
server/middleware/
├── 📄 cors.ts           # CORS handling
└── 📄 auth.ts          # Auth verification
```

### 3.3 server/utils/ - Server Utilities

```
server/utils/
└── 📄 db.ts            # Database connection
```

---

## 4. Các File Cấu Hình

### 4.1 nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // Bắt buộc cho Nuxt 4
  compatibilityDate: '2025-07-15',

  // DevTools
  devtools: { enabled: true },

  // Modules
  modules: [],

  // App config
  app: {
    head: {
      title: 'My App',
      meta: [
        { charset: 'utf-8' },
        { name: 'viewport', content: 'width=device-width, initial-scale=1' }
      ]
    }
  }
})
```

### 4.2 app.config.ts

```typescript
// app.config.ts
export default defineAppConfig({
  // Runtime configuration
  title: 'My App',
  
  // Custom config
  theme: {
    primaryColor: '#42b883'
  },

  // Public runtime config
  public: {
    apiBase: '/api'
  }
})
```

### 4.3 Sự khác nhau nuxt.config vs app.config

```
┌─────────────────────────────────────────────────────────────────────┐
│                    nuxt.config vs app.config                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  nuxt.config.ts                                                    │
│  ──────────────────                                                │
│  • Cấu hình BUILD TIME (khi dev/build)                           │
│  • Thay đổi → phải restart dev server                           │
│  • Ví dụ: modules, plugins, devtools                              │
│                                                                     │
│  app.config.ts                                                     │
│  ──────────────                                                    │
│  • Cấu hình RUNTIME (khi chạy)                                   │
│  • Thay đổi → auto-update khi dev (thường)                       │
│  • Ví dụ: site title, theme, feature flags                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Thư Mục public/

### public/ - Static Files

```
public/ STRUCTURE:
───────────────────

public/
├── 📄 favicon.ico       # Favicon
├── 📄 robots.txt        # SEO
├── 📄 sitemap.xml       # SEO
└── 📁 images/
    ├── 📄 logo.png
    └── 📄 hero.jpg
```

**Lưu ý:**
- Files trong `public/` được serve trực tiếp
- URL = file path (VD: `/images/logo.png`)
- Copy as-is, không build

---

## 6. Quy Tắc Đặt Tên

### 6.1 Vue Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPONENT NAMING CONVENTIONS                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  File Name                  │  Component Name    │  Usage        │
│  ────────────────────────────────────────────────────────────────── │
│  Header.vue                 │  <Header>         │  Direct      │
│  header.vue                 │  <Header>         │  Direct      │
│                                                                      │
│  ui/Modal.vue              │  <UiModal>        │  With prefix │
│  base/Button.vue          │  <BaseButton>      │  With prefix │
│  Base/Button.vue          │  <BaseButton>      │  With prefix │
│                                                                      │
│  myButton.vue             │  <MyButton>        │  PascalCase  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.2 Composables

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPOSABLE NAMING CONVENTIONS                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  File Name                  │  Function Name     │  Usage          │
│  ────────────────────────────────────────────────────────────────── │
│  useAuth.ts                │  useAuth()        │  Must start     │
│  useTheme.ts               │  useTheme()       │  with "use"     │
│  useFetchUser.ts          │  useFetchUser()   │                │
│                                                                      │
│  ❌ auth.ts                │  Không work!      │  Missing "use" │
│  ❌ useAuthStore.ts        │  Không work!      │  Extra stuff  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.3 Pinia Stores

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STORE NAMING CONVENTIONS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  File Name                  │  Function Name     │  Usage          │
│  ────────────────────────────────────────────────────────────────── │
│  auth.ts                    │  useAuthStore()   │  Must end with  │
│  cart.ts                   │  useCartStore()   │  "Store"        │
│  product.ts                │  useProductStore() │               │
│                                                                      │
│  ❌ authStore.ts           │  Không work!      │  Extra "Store" │
│  ❌ useAuth.ts            │  Không work!      │  Missing "Store"│
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.4 API Routes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    API ROUTE NAMING CONVENTIONS                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  File Name                  │  Method + Path                        │
│  ────────────────────────────────────────────────────────────────── │
│  users.get.ts              │  GET /api/users                        │
│  users.post.ts             │  POST /api/users                       │
│  users/[id].get.ts        │  GET /api/users/:id                    │
│  users/[id].put.ts        │  PUT /api/users/:id                    │
│  users/[id].delete.ts     │  DELETE /api/users/:id                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    QUICK REFERENCE                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  app/pages/     → File-based routing                               │
│  app/components/ → Reusable components (auto-imported)              │
│  app/composables/ → Reusable logic (auto-imported)                  │
│  app/stores/    → Pinia stores (auto-imported)                      │
│  app/layouts/   → Page layouts                                      │
│  app/middleware/ → Route guards                                    │
│  app/plugins/   → App initialization                               │
│  app/utils/    → Utility functions (auto-imported)                  │
│  server/api/    → API endpoints                                    │
│  public/       → Static files                                      │
│                                                                     │
│  NAMING:                                                            │
│  ├── Components: PascalCase (Button.vue → <Button>)                │
│  ├── Composables: useXXX (useAuth.ts → useAuth())                  │
│  ├── Stores: useXXXStore (auth.ts → useAuthStore())                │
│  └── API: METHOD.path.ts (users.get.ts → GET /api/users)           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Học về Auto-imports System

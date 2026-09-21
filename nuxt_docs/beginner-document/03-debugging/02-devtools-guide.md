# Hướng Dẫn Nuxt DevTools

> **Mục tiêu:** Học cách sử dụng Nuxt DevTools để debug và phát triển nhanh hơn.

## Mục lục

1. [Nuxt DevTools là gì?](#1-nuxt-devtools-là-gì)
2. [Bật/Tắt DevTools](#2-bậttắt-devtools)
3. [Giao diện DevTools](#3-giao-diện-devtools)
4. [Các tính năng chính](#4-các-tính-năng-chính)
5. [Mẹo sử dụng](#5-mẹo-sử-dụng)

---

## 1. Nuxt DevTools Là Gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT DEVTOOLS                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nuxt DevTools = Công cụ PHÁT TRIỂN trong TRÌNH DUYỆT           │
│                                                                     │
│  Giống như Vue DevTools, nhưng DÀNH RIÊNG cho Nuxt              │
│                                                                     │
│  ĐƯỢC TÍCH HỢP SẴN trong Nuxt 3/4!                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh với các công cụ khác

| Công cụ | Mục đích | Cách mở |
|---------|-----------|----------|
| **Nuxt DevTools** | Debug Nuxt app | Shift + D |
| **Vue DevTools** | Debug Vue components | Shift + V |
| **Browser DevTools** | Debug HTML/CSS/JS | F12 |

---

## 2. Bật/Tắt DevTools

### 2.1 Bật DevTools (Mặc định đã bật)

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  devtools: { enabled: true }  // Mặc định là true
})
```

### 2.2 Tắt DevTools

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  devtools: { enabled: false }
})
```

### 2.3 Cách mở DevTools

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CÁCH MỞ NUXT DEVTOOLS                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Chạy dev server:                                              │
│     bun run dev                                                    │
│                                                                     │
│  2. Mở trình duyệt:                                              │
│     http://localhost:3000                                         │
│                                                                     │
│  3. Nhấn tổ hợp phím:                                            │
│     Windows/Linux: Shift + D                                       │
│     Mac: Shift + D                                                 │
│                                                                     │
│  4. DevTools hiện ra ở góc dưới/phải màn hình                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Giao Diện DevTools

### Giao diện chính

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT DEVTOOLS                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────┬─────────────────────────────────────────────┐    │
│  │             │                                              │    │
│  │  📊 Pages  │   NỘI DUNG CHÍNH                           │    │
│  │  🧩 Components │                                          │    │
│  │  📦 Pinia   │   (Thay đổi theo tab được chọn)            │    │
│  │  🌐 Routes  │                                              │    │
│  │  📁 Modules │                                              │    │
│  │  ⚡ Inspector│                                              │    │
│  │  📋 Timeline│                                              │    │
│  │             │                                              │    │
│  └─────────────┴─────────────────────────────────────────────┘    │
│                                                                     │
│  STATUS BAR:                                                     │
│  ✓ Nuxt 4.5.2 | 📁 app/pages | 🔥 SSR Mode | ⚡ Fast Refresh    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Các Tính Năng Chính

### 4.1 Tab Pages - Xem Routes

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PAGES TAB                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Hiển thị TẤT CẢ routes trong ứng dụng                         │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ Routes                                                       │    │
│  │ ────────────────────────────────────────────────────────    │    │
│  │                                                             │    │
│  │ ▶ /                    pages/index.vue                  │    │
│  │ ▶ /about              pages/about.vue                  │    │
│  │ ▶ /blog               pages/blog/index.vue             │    │
│  │   └── /blog/:slug     pages/blog/[slug].vue           │    │
│  │ ▶ /admin              pages/admin/index.vue            │    │
│  │   └── /admin/users    pages/admin/users.vue            │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  Actions:                                                         │
│  • Click vào route → Navigate đến route đó                      │
│  • Xem file path của route                                        │
│  • Xem nested routes                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 Tab Components - Inspect Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                      COMPONENTS TAB                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Hiển thị COMPONENT TREE của page hiện tại                      │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ App                                                               │    │
│  │ ▼ DefaultLayout                                                  │    │
│  │   ▼ Header                                                       │    │
│  │     ├── NuxtLink (Logo)                                     │    │
│  │     └── Nav                                                      │    │
│  │       ├── NuxtLink (Home)                                  │    │
│  │       ├── NuxtLink (Blog)                                  │    │
│  │       └── NuxtLink (About)                                 │    │
│  │   ▼ NuxtPage                                                    │    │
│  │     ▼ BlogIndex                                                │    │
│  │       ├── PostCard                                          │    │
│  │       ├── PostCard                                          │    │
│  │       └── PostCard                                          │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  Actions:                                                         │
│  • Click vào component → Inspect props, state                    │
│  • Hover → Highlight component trên page                          │
│  • Copy component name                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.3 Tab Pinia - State Management

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PINIA TAB                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Hiển thị TẤT CẢ Pinia stores và state hiện tại               │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ Stores                                                       │    │
│  │ ────────────────────────────────────────────────────────    │    │
│  │                                                             │    │
│  │ ▼ auth (stores/auth.ts)                                     │    │
│  │   ├── user: { id: 1, name: "Nam" }                     │    │
│  │   ├── isAuthenticated: true                            │    │
│  │   └── token: "eyJhbG..."                               │    │
│  │                                                             │    │
│  │ ▶ cart (stores/cart.ts)                                    │    │
│  │   ├── items: [...]                                       │    │
│  │   ├── total: 150000                                    │    │
│  │   └── itemCount: 3                                      │    │
│  │                                                             │    │
│  │ ▶ settings (stores/settings.ts)                           │    │
│  │   ├── theme: "dark"                                    │    │
│  │   └── language: "vi"                                    │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  Actions:                                                         │
│  • Click để expand/collapse store                               │
│  • Xem state hiện tại                                           │
│  • Click state → Edit trực tiếp (dev only!)                    │
│  • Thực hiện actions                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.4 Tab Routes - Route Inspector

```
┌─────────────────────────────────────────────────────────────────────┐
│                       ROUTES TAB                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Hiển thị THÔNG TIN chi tiết của route hiện tại               │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ Current Route: /blog/vue3-composition-api               │    │
│  │ ──────────────────────────────────────────────────────    │    │
│  │                                                             │    │
│  │ Path: /blog/:slug                                       │    │
│  │ Component: pages/blog/[slug].vue                       │    │
│  │                                                             │    │
│  │ Params:                                                   │    │
│  │   slug: "vue3-composition-api"                         │    │
│  │                                                             │    │
│  │ Query: {}                                                  │    │
│  │                                                             │    │
│  │ Meta:                                                      │    │
│  │   middleware: ["auth"]                                   │    │
│  │   layout: "default"                                    │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  Actions:                                                         │
│  • Xem params hiện tại                                           │
│  • Xem query strings                                             │
│  • Xem middleware áp dụng                                        │
│  • Xem layout đang dùng                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.5 Tab Modules - Nuxt Modules

```
┌─────────────────────────────────────────────────────────────────────┐
│                       MODULES TAB                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Hiển thị TẤT CẢ Nuxt modules đã cài đặt                     │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ Installed Modules                                           │    │
│  │ ────────────────────────────────────────────────────────    │    │
│  │                                                             │    │
│  │ 📦 @nuxtjs/color-mode                                    │    │
│  │    Version: 3.3.0                                        │    │
│  │    Status: ✅ Active                                     │    │
│  │                                                             │    │
│  │ 📦 @nuxtjs/google-fonts                                  │    │
│  │    Version: 1.2.0                                        │    │
│  │    Status: ✅ Active                                     │    │
│  │                                                             │    │
│  │ 📦 @pinia/nuxt                                           │    │
│  │    Version: 0.5.0                                        │    │
│  │    Status: ✅ Built-in                                   │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.6 Tab Timeline - Performance

```
┌─────────────────────────────────────────────────────────────────────┐
│                      TIMELINE TAB                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Xem PERFORMANCE của app qua thời gian                           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ Timeline                                                     │    │
│  │ ────────────────────────────────────────────────────────    │    │
│  │                                                             │    │
│  │ [====] [==========] [=======] [====] [=========]         │    │
│  │  Start    Page Load   Render   Hydrate   Ready          │    │
│  │                                                             │    │
│  │                                                             │    │
│  │ Metrics:                                                   │    │
│  │ • TTFB: 45ms                                              │    │
│  │ • FCP: 120ms                                              │    │
│  │ • LCP: 340ms                                              │    │
│  │ • CLS: 0.01                                               │    │
│  │                                                             │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Mẹo Sử Dụng

### 5.1 Quick Open (Mở nhanh)

```
┌─────────────────────────────────────────────────────────────────────┐
│                        QUICK OPEN                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhấn Shift + D để mở DevTools                                 │
│                                                                     │
│  Khi DevTools đang mở:                                           │
│  • Nhấn P → Quick open Pages                                    │
│  • Nhấn C → Quick open Components                               │
│  • Nhấn S → Quick open Stores                                  │
│  • Nhấn Esc → Đóng DevTools                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Pinch to Zoom

```
┌─────────────────────────────────────────────────────────────────────┐
│                        PINCH TO ZOOM                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  DevTools có thể resize bằng cách:                              │
│  • Kéo edges/corners để resize                                  │
│  • Double-click để maximize/restore                             │
│  • Nhấn X để đóng                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.3 Debug State Changes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DEBUG STATE CHANGES                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Mở DevTools → Tab Pinia                                      │
│  2. Click vào store muốn debug                                  │
│  3. Thực hiện action (VD: login, add to cart)                  │
│  4. Xem state thay đổi trong real-time                          │
│  5. Click vào state để EDIT TRỰC TIẾP (dev only!)              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.4 Copy Component/Store Names

```
┌─────────────────────────────────────────────────────────────────────┐
│                        COPY NAMES                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Click chuột phải vào:                                           │
│  • Component → Copy component name                               │
│  • Store → Copy store name                                       │
│  • Route → Copy route path                                       │
│                                                                     │
│  Rất hữu ích khi debug hoặc viết tests!                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DEVTOOLS CHEAT SHEET                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  MỞ DEVTOOLS:     Shift + D                                       │
│                                                                     │
│  PAGES TAB:        Xem tất cả routes                              │
│  COMPONENTS TAB:   Inspect component tree                         │
│  PINIA TAB:        Debug state management                         │
│  ROUTES TAB:       Xem route details, params, middleware          │
│  MODULES TAB:      Xem installed modules                          │
│  TIMELINE TAB:     Xem performance metrics                       │
│                                                                     │
│  SHORTCUTS:                                                         │
│  P → Quick open Pages                                            │
│  C → Quick open Components                                        │
│  S → Quick open Stores                                           │
│  Esc → Close DevTools                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [03-debugging-tips.md](03-debugging-tips.md) - Mẹo Debug Nâng Cao

hoặc → [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Quay lại học Core Concepts

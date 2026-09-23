# Nuxt 3 → Nuxt 4 - Hướng Dẫn Nâng Cấp

> **Mục tiêu:** Hiểu sự khác biệt giữa Nuxt 3 và Nuxt 4 và cách nâng cấp.

## Mục lục

1. [Nuxt 3 vs Nuxt 4](#1-nuxt-3-vs-nuxt-4)
2. [Thay đổi chính](#2-thay-đổi-chính)
3. [Migration Guide](#3-migration-guide)

---

## 1. Nuxt 3 vs Nuxt 4

### 1.1 Thay đổi lớn nhất

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT 3 → NUXT 4                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NUXT 3:                                                          │
│  ├── /pages, /components, /composables (root level)             │
│  └── app/ folder là optional                                     │
│                                                                     │
│  NUXT 4:                                                          │
│  ├── /app/pages, /app/components, /app/composables              │
│  └── app/ folder là BẮT BUỘC                                    │
│                                                                     │
│  ⚠️ Breaking change: Cấu trúc thư mục thay đổi!               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 So sánh cấu trúc

```
NUXT 3:                      NUXT 4:
─────────                     ─────────
/pages/                     /app/pages/
/components/               /app/components/
/composables/              /app/composables/
/layouts/                  /app/layouts/
/middleware/               /app/middleware/
/plugins/                  /app/plugins/
/stores/                   /app/stores/
/assets/                   /app/assets/
/utils/                    /app/utils/

(none)                     /app/app.vue
/server/                   /server/
/public/                   /public/
/nuxt.config.ts            /nuxt.config.ts
```

---

## 2. Thay Đổi Chính

### 2.1 app/ folder bắt buộc

```bash
# Tạo cấu trúc Nuxt 4
mkdir -p app/pages app/components app/composables app/layouts
```

### 2.2 nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-07-15', // Required in Nuxt 4
  // ... rest of config
})
```

### 2.3 Không còn pages/ root level

```bash
# ❌ Nuxt 4 - Không hoạt động
/pages/index.vue

# ✅ Nuxt 4
/app/pages/index.vue
```

---

## 3. Migration Guide

### Bước 1: Backup project hiện tại

```bash
# Backup
cp -r my-nuxt3-app my-nuxt3-app-backup
```

### Bước 2: Tạo app/ folder

```bash
# Di chuyển vào app/
mkdir app
mv pages components composables layouts middleware plugins stores assets utils app/
```

### Bước 3: Tạo app.vue

```vue
<!-- app/app.vue -->
<template>
  <div>
    <NuxtRouteAnnouncer />
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
```

### Bước 4: Thêm compatibilityDate

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2024-07-15',
  // ... rest
})
```

### Bước 5: Test

```bash
npm run dev
```

---

## ▶️ Tham Khảo

→ [Nuxt 4 Migration](https://nuxt.com/docs/getting-started/upgrade#upgrading-to-nuxt-4) - Hướng dẫn chi tiết

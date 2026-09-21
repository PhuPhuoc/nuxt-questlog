# Migration: Nuxt 3 → Nuxt 4

> **Mục tiêu:** Hướng dẫn chuyển từ Nuxt 3 lên Nuxt 4.

## Mục lục

1. [Tóm tắt thay đổi](#1-tóm-tắt-thay-đổi)
2. [Thư mục app/](#2-thư-mục-app)
3. [Pinia được tích hợp sẵn](#3-pinia-được-tích-hợp-sẵn)
4. [compatibilityDate](#4-compatibilitydate)
5. [Breaking Changes](#5-breaking-changes)
6. [Migration Checklist](#6-migration-checklist)

---

## 1. Tóm Tắt Thay Đổi

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT 3 → NUXT 4                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ app/ directory là MẶC ĐỊNH                                   │
│  ✅ Pinia được tích hợp sẵn (không cần module)              │
│  ✅ compatibilityDate bắt buộc                                    │
│  ✅ TypeScript inference tốt hơn                                 │
│  ✅ DevTools cải thiện                                           │
│                                                                     │
│  ⚠️ Breaking changes:                                              │
│  ├── Pages ở app/ thay vì root                                  │
│  ├── Cần compatibilityDate                                        │
│  └── Một số deprecations                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Thư Mục app/

### 2.1 Thay đổi cấu trúc

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT 3                              NUXT 4          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  # Cấu trúc Nuxt 3:                                            │
│  📁 root/                                                       │
│  ├── 📁 pages/         →         📁 app/pages/                │
│  ├── 📁 components/    →         📁 app/components/           │
│  ├── 📁 composables/   →         📁 app/composables/          │
│  └── nuxt.config.ts   →         nuxt.config.ts               │
│                                                                     │
│  # Cấu trúc Nuxt 4:                                            │
│  📁 root/                                                       │
│  └── 📁 app/                                                    │
│      ├── 📁 pages/                                              │
│      ├── 📁 components/                                         │
│      └── ...                                                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 Di chuyển files

```bash
# Di chuyển từng thư mục
mv pages app/pages
mv components app/components
mv composables app/composables
mv layouts app/layouts
mv middleware app/middleware
mv plugins app/plugins
mv utils app/utils
mv stores app/stores
```

### 2.3 Hoặc giữ cấu trúc cũ

```typescript
// nuxt.config.ts
// Nếu muốn giữ cấu trúc Nuxt 3:

export default defineNuxtConfig({
  future: {
    compatibilityVersion: 3  // Giữ cấu trúc cũ
  },
  compatibilityDate: '2025-07-15'
})
```

---

## 3. Pinia Được Tích Hợp Sẵn

### 3.1 Nuxt 3 - Cần setup

```typescript
// nuxt.config.ts (Nuxt 3)
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
  pinia: {
    storesDirs: ['./stores/**']
  }
})
```

```typescript
// stores/auth.ts
import { defineStore } from 'pinia'

export const useAuthStore = defineStore('auth', () => {
  // Store code
})
```

### 3.2 Nuxt 4 - Không cần setup

```typescript
// nuxt.config.ts (Nuxt 4)
export default defineNuxtConfig({
  // Không cần modules: ['@pinia/nuxt']!
  compatibilityDate: '2025-07-15'
})
```

```typescript
// app/stores/auth.ts
// Tự động được auto-import!
export const useAuthStore = defineStore('auth', () => {
  // Store code
})
```

### 3.3 Thay đổi tên thư mục

```bash
# Nuxt 3
mv stores app/stores

# Hoặc giữ nguyên (vẫn hoạt động)
```

---

## 4. compatibilityDate

### 4.1 Thêm compatibilityDate

```typescript
// nuxt.config.ts (Nuxt 4)
export default defineNuxtConfig({
  compatibilityDate: '2025-07-15',  // ← BẮT BUỘC
  devtools: { enabled: true }
})
```

### 4.2 Ý nghĩa của compatibilityDate

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPATIBILITY DATE                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  compatibilityDate = "Ràng buộc" với một thời điểm           │
│                                                                     │
│  • Nuxt sẽ sử dụng behavior của phiên bản tại ngày đó         │
│  • Giúp app của bạn ổn định khi Nuxt update                   │
│  • Bạn có thể chọn ngày mới khi muốn adopt features mới       │
│                                                                     │
│  VD: '2025-07-15' = Dùng behavior của Nuxt ngày 15/07/2025     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Breaking Changes

### 5.1 Auto-imports thay đổi

```typescript
// Tất cả composables/utils phải trong app/
// Không còn auto-import từ root
```

### 5.2 Vue APIs

```typescript
// Một số Vue 3.5+ features yêu cầu compatibilityDate mới

// VD: defineModel (Vue 3.5+) yêu cầu recent compatibilityDate
```

### 5.3 Server routes

```typescript
// Server routes vẫn ở server/
// Không thay đổi
```

---

## 6. Migration Checklist

### Trước khi migrate

```
□ Backup project
□ Kiểm tra Nuxt 3 hoạt động tốt
□ Note lại custom configurations
```

### Migration steps

```
□ 1. Update Nuxt: npm install nuxt@latest
□ 2. Tạo thư mục app/
□ 3. Di chuyển pages → app/pages
□ 4. Di chuyển components → app/components
□ 5. Di chuyển composables → app/composables
□ 6. Di chuyển layouts → app/layouts
□ 7. Di chuyển stores → app/stores
□ 8. Di chuyển plugins → app/plugins
□ 9. Di chuyển middleware → app/middleware
□ 10. Di chuyển utils → app/utils
□ 11. Thêm compatibilityDate vào nuxt.config.ts
□ 12. Xóa @pinia/nuxt khỏi modules (nếu có)
□ 13. Chạy dev server và test
□ 14. Fix breaking changes nếu có
```

### Sau khi migrate

```
□ Test tất cả routes
□ Test tất cả components
□ Test Pinia stores
□ Test SSR
□ Test DevTools
```

---

## ▶️ Tiếp Theo

→ [03-common-gotchas.md](03-common-gotchas.md) - Những bẫy thường gặp

hoặc → [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Quay lại học Core Concepts

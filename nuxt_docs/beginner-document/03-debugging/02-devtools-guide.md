# Nuxt DevTools - Công Cụ Debug Mạnh Mẽ

> **Mục tiêu:** Biết cách sử dụng Nuxt DevTools để debug ứng dụng.

## Mục lục

1. [Giới thiệu DevTools](#1-giới-thiệu-devtools)
2. [Bật/Tắt DevTools](#2-bậttắt-devtools)
3. [Tính năng chính](#3-tính-năng-chính)

---

## 1. Giới Thiệu DevTools

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT DEVTOOLS                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  DevTools = Công cụ debug tích hợp trong trình duyệt             │
│                                                                     │
│  Mở: Nhấn Shift + D trong trình duyệt khi dev server chạy       │
│                                                                     │
│  Các tab chính:                                                    │
│  ├── Pages - Xem routes                                           │
│  ├── Components - Component tree                                   │
│  ├── Pinia - State management                                     │
│  ├── Imports - Auto-imports                                       │
│  └── Modules - Installed modules                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Bật/Tắt DevTools

### 2.1 Trong nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  devtools: { 
    enabled: true  // Bật DevTools
    // enabled: false  // Tắt DevTools
  }
})
```

### 2.2 Cách mở

```bash
# Chạy dev server
npm run dev

# Mở trình duyệt
# Nhấn Shift + D (hoặc Shift + I)
```

---

## 3. Tính Năng Chính

### 3.1 Pages Tab

- Xem tất cả routes
- Navigate giữa pages
- Xem route params

### 3.2 Components Tab

- Component tree visualization
- Xem component props
- Xem file location

### 3.3 Pinia Tab

- Xem tất cả stores
- Inspect state
- Xem getters/actions

### 3.4 Timeline

- Xem render timing
- Track performance

---

## ▶️ Tham Khảo

- Docs: [devtools.nuxt.com](https://devtools.nuxt.com)

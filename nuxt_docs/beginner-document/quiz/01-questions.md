# Questions & Answers - Câu Hỏi Thường Gặp

> **Mục tiêu:** Trả lời những câu hỏi phổ biến về Nuxt.

## Mục lục

1. [Installation](#1-installation)
2. [Routing](#2-routing)
3. [Data Fetching](#3-data-fetching)
4. [Components](#4-components)

---

## 1. Installation

### Q: Nuxt 3 hay Nuxt 4?

**A:** Nếu bạn bắt đầu project mới, dùng **Nuxt 4**. Nuxt 4 có:
- Cấu trúc thư mục mới (`app/` folder)
- Tốt hơn về performance
- Support lâu dài hơn

### Q: Cần cài gì trước?

**A:** 
1. Node.js 18+
2. Bun hoặc npm
3. VS Code với Vue extension

---

## 2. Routing

### Q: Làm sao tạo dynamic route?

**A:** Dùng bracket notation:

```
pages/
├── users/
│   └── [id].vue    → /users/:id
└── blog/
    └── [slug].vue → /blog/:slug
```

### Q: Làm sao get params từ URL?

**A:** Dùng `useRoute()`:

```typescript
const route = useRoute()
const id = route.params.id  // /users/123 → "123"
```

---

## 3. Data Fetching

### Q: useFetch vs $fetch?

**A:** 
- `useFetch`: GET với auto-caching, SSR support
- `$fetch`: Mutations, one-off requests

### Q: Làm sao refresh data?

**A:** 

```typescript
const { data, refresh } = await useFetch('/api/posts')

// Gọi lại
await refresh()
```

---

## 4. Components

### Q: Component không được auto-import?

**A:** Kiểm tra:
1. File trong `app/components/`
2. Tên đúng PascalCase
3. Dev server đang chạy

### Q: Làm sao truyền data giữa components?

**A:** 
- Props: Parent → Child
- Emits: Child → Parent
- Pinia/useState: Chia sẻ state

---

## ▶️ Hỏi Thêm?

Tham gia [Nuxt Community](https://discord.gg/nuxt)

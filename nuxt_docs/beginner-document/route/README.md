# Hệ Thống Routing Trong Nuxt

## 📚 Mục lục Tài Liệu

### Lộ Trình Học Routing

| Chapper | Nội dung | Độ dài |
|---------|----------|---------|
| [01-overview.md](01-overview.md) | So sánh Nuxt vs Vue Router | ~15 phút |
| [02-basic-routes.md](02-basic-routes.md) | Static, Dynamic, Catch-all routes | ~20 phút |
| [03-nested-routes.md](03-nested-routes.md) | Nested routes & Layouts | ~15 phút |
| [04-navigation.md](04-navigation.md) | NuxtLink, navigateTo, useRouter | ~15 phút |
| [05-middleware.md](05-middleware.md) | Middleware & Route Guards | ~25 phút |
| [06-advanced.md](06-advanced.md) | Validation, Interceptors, Lifecycle | ~25 phút |
| [07-exercises.md](07-exercises.md) | Bài tập thực hành | ~30 phút |

---

## 🎯 Tổng Quan

### Bạn sẽ học được gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ROUTING MASTERY                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ File-based Routing                                             │
│     ├── Static routes (index.vue, about.vue)                       │
│     ├── Dynamic routes ([id].vue, [category]-[id].vue)              │
│     ├── Catch-all routes ([...slug].vue)                           │
│     └── Optional routes ([[lang]].vue)                             │
│                                                                     │
│  ✅ Navigation                                                     │
│     ├── <NuxtLink> với prefetching                               │
│     ├── navigateTo() global utility                                │
│     └── useRouter() composable                                     │
│                                                                     │
│  ✅ Middleware                                                     │
│     ├── Global middleware (.global.ts)                             │
│     ├── Named middleware                                           │
│     ├── Anonymous middleware                                        │
│     └── Auth guards, role-based access                             │
│                                                                     │
│  ✅ Advanced Topics                                                │
│     ├── Route validation                                           │
│     ├── Data fetching patterns                                     │
│     ├── SSR vs Client navigation                                   │
│     └── Navigation lifecycle hooks                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Bắt Đầu

### Requisites
- Đã hoàn thành Module 1-2 (Cài đặt & Components)
- Hiểu cơ bản về Vue Router (nếu có)

### Thứ tự học đề xuất

```
TUẦN 1:
├── Day 1: Đọc [01-overview.md] - Hiểu sự khác biệt
├── Day 2: Đọc [02-basic-routes.md] - Thực hành tạo routes
└── Day 3-4: Đọc [03-nested-routes.md] + [04-navigation.md]

TUẦN 2:
├── Day 5-6: Đọc [05-middleware.md] - Quan trọng!
└── Day 7: Đọc [06-advanced.md] + [07-exercises.md]
```

---

## 📁 Cấu Trúc File Trong `app/pages/`

```
📁 app/pages/
│
├── 📄 index.vue                    →  /
├── 📄 about.vue                   →  /about
├── 📄 contact.vue                 →  /contact
│
├── 📁 blog/
│   ├── 📄 index.vue              →  /blog
│   ├── 📄 [slug].vue            →  /blog/:slug
│   └── 📄 [year]/[month].vue   →  /blog/:year/:month
│
├── 📁 user/
│   ├── 📄 index.vue              →  /user
│   ├── 📄 [id].vue              →  /user/:id
│   ├── 📄 [id]/
│   │   ├── 📄 posts.vue         →  /user/:id/posts
│   │   └── 📄 settings.vue      →  /user/:id/settings
│
├── 📄 [...slug].vue              →  /* (catch-all)
└── 📄 [[lang]].vue              →  / hoặc /en, /vi (optional)
```

---

## 🔑 Cheat Sheet Nhanh

```vue
<!-- 1. Navigation -->
<NuxtLink to="/about">Link</NuxtLink>
await navigateTo('/dashboard')
await navigateTo('/user', { replace: true })

<!-- 2. Route Info -->
const route = useRoute()
route.params.id        // Dynamic segment
route.query.tab        // Query params

<!-- 3. Middleware -->
definePageMeta({
  middleware: 'auth'           // Named
  middleware: [(to) => {}]     // Anonymous
})

<!-- 4. Validation -->
definePageMeta({
  validate: (route) => true // hoặc false / object
})
```

---

## 🔗 Liên Kết

- [Nuxt Routing Docs](https://nuxt.com/docs/getting-started/routing)
- [Vue Router Docs](https://router.vuejs.org/)
- [Nuxt Middleware](https://nuxt.com/docs/guide/directory-structure/middleware)

---

*Tài liệu này là phần của Lộ Trình Học Nuxt 4*

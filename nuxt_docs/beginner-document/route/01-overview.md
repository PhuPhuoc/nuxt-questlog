# Routing Module - Định Tuyến Trong Nuxt

> **Mục tiêu:** Tổng hợp kiến thức về routing trong Nuxt.

## Mục lục

1. [Overview](#1-overview)
2. [Quick Reference](#2-quick-reference)

---

## 1. Overview

Nuxt sử dụng **file-based routing**:
- File trong `pages/` = Route
- Tên file = Path URL
- `[param].vue` = Dynamic segment

---

## 2. Quick Reference

```
pages/index.vue → /
pages/about.vue → /about
pages/[slug].vue → /:slug
```

### Navigation
```vue
<NuxtLink to="/">Home</NuxtLink>
router.push('/about')
```

### Route Info
```typescript
const route = useRoute()
route.params.slug
route.query.page
```

---

## ▶️ Chi Tiết

Xem các bài trong module này:
- [Basic Routes](02-basic-routes.md)
- [Nested Routes](03-nested-routes.md)
- [Navigation](04-navigation.md)
- [Middleware](05-middleware.md)

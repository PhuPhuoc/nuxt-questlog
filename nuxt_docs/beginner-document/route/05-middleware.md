# Middleware - Bảo Vệ Routes

> **Mục tiêu:** Sử dụng middleware để bảo vệ routes.

## Route Middleware

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const isLoggedIn = useAuthStore().isAuthenticated
  
  if (!isLoggedIn) {
    return navigateTo('/login')
  }
})
```

## Áp dụng cho Page

```vue
<script setup lang="ts">
definePageMeta({
  middleware: 'auth'
})
</script>
```

## Global Middleware

```typescript
// app/middleware/auth.global.ts
// Tự động chạy trên mọi route
```

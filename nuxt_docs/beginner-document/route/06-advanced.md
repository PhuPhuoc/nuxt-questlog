# Chapper 6: Advanced Topics - Validation, Interceptors & Lifecycle

## Mục lục

1. [Route Validation](#1-route-validation)
2. [definePageMeta Chi Tiết](#2-definepagemeta-chi-tiết)
3. [Navigation Interceptors](#3-navigation-interceptors)
4. [Routing Lifecycle Hooks](#4-routing-lifecycle-hooks)
5. [Error Pages](#5-error-pages)

---

## 1. Route Validation

### Giới Thiệu

Route validation cho phép bạn kiểm tra route params trước khi render page:

```vue
<script setup>
definePageMeta({
  validate: async (route) => {
    // Return true = cho phép
    // Return false = 404 page
    // Return object = custom error
  }
})
</script>
```

### Ví Dụ Cơ Bản

```vue
<!-- app/pages/user/[id].vue -->
<script setup>
definePageMeta({
  validate: (route) => {
    // ID phải là số
    return /^\d+$/.test(route.params.id)
  }
})
</script>
```

### Return Values

| Return | Kết quả |
|--------|----------|
| `true` | Cho phép, render page |
| `false` | 404 Not Found |
| `object` | Custom error (statusCode, statusMessage) |

### Ví Dụ: Validate UUID

```vue
<!-- app/pages/product/[id].vue -->
<script setup>
definePageMeta({
  validate: (route) => {
    const id = route.params.id

    // UUID regex
    const uuidRegex = /^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i

    if (!uuidRegex.test(id)) {
      return {
        statusCode: 400,
        statusMessage: 'Invalid product ID format'
      }
    }

    return true
  }
})

const route = useRoute()
const { data: product } = await useFetch(`/api/products/${route.params.id}`)
</script>
```

### Async Validation

```vue
<!-- app/pages/blog/[slug].vue -->
<script setup>
definePageMeta({
  validate: async (route) => {
    const slug = route.params.slug

    // Kiểm tra database
    const exists = await $fetch(`/api/posts/exists/${slug}`)

    if (!exists) {
      return false  // 404
    }

    return true
  }
})
</script>
```

### Multiple Params Validation

```vue
<!-- app/pages/product/[category]/[id].vue -->
<script setup>
definePageMeta({
  validate: (route) => {
    const { category, id } = route.params

    // Validate category
    const validCategories = ['electronics', 'clothing', 'books']
    if (!validCategories.includes(category)) {
      return false  // 404
    }

    // Validate ID
    const idRegex = /^\d+$/
    if (!idRegex.test(id)) {
      return {
        statusCode: 400,
        statusMessage: 'ID must be a number'
      }
    }

    return true
  }
})
</script>
```

---

## 2. definePageMeta Chi Tiết

### Full Options

```vue
<script setup>
definePageMeta({
  // Middleware
  middleware: [
    'auth',                    // Named middleware
    (to) => { /* inline */ } // Anonymous middleware
  ],

  // Layout
  layout: 'default',           // Layout name
  layout: false,              // Disable layout

  // Validation
  validate: (route) => true,

  // SEO
  title: 'Page Title',
  name: 'page-name',

  // Custom meta
  alias: ['/old-path'],
  pageTransition: { name: 'page', mode: 'out-in' },
  keepalive: true
})
</script>
```

### SEO Meta

```vue
<script setup>
definePageMeta({
  title: 'Trang chủ',
  description: 'Mô tả trang',
  // hoặc dùng useSeoMeta() trong script
})
</script>
```

### Page Transitions

```vue
<script setup>
definePageMeta({
  pageTransition: {
    name: 'slide',
    mode: 'out-in'
  }
})
</script>

<style>
.slide-enter-active,
.slide-leave-active {
  transition: all 0.2s;
}
.slide-enter-from {
  opacity: 0;
  transform: translateX(20px);
}
.slide-leave-to {
  opacity: 0;
  transform: translateX(-20px);
}
</style>
```

### Keepalive

```vue
<script setup>
// Giữ component alive khi rời khỏi page
definePageMeta({
  keepalive: true
  // hoặc với options
  keepalive: {
    include: ['component-name']
  }
})
</script>
```

---

## 3. Navigation Interceptors

### useRouter Hooks

```typescript
// app/composables/useNavigationHooks.ts
export const useNavigationHooks = () => {
  const router = useRouter()

  // Trước khi navigation
  const unsubsribe = router.beforeEach((to, from) => {
    console.log('Going to:', to.path)

    // Có thể:
    // - return false → cancel navigation
    // - return '/path' → redirect
  })

  // Sau khi navigation
  router.afterEach((to, from, failure) => {
    if (failure) {
      console.error('Navigation failed:', failure)
    } else {
      console.log('Navigated to:', to.path)
    }
  })

  // Cleanup
  onUnmounted(() => {
    unsubsribe()
  })
}
```

### Interceptors Trong Plugin

```typescript
// app/plugins/router.ts
export default defineNuxtPlugin((nuxtApp) => {
  const router = useRouter()

  router.beforeEach((to, from) => {
    // Analytics
    trackPageView(to.path)

    // Loading state
    showLoading()

    return true  // Cho phép navigation
  })

  router.afterEach(() => {
    hideLoading()
  })
})
```

### Cancel Navigation

```typescript
router.beforeEach((to, from) => {
  // Cancel nếu form chưa save
  if (hasUnsavedForm.value) {
    const confirmed = confirm('You have unsaved changes. Leave?')
    if (!confirmed) {
      return false  // Cancel navigation
    }
  }

  return true
})
```

### Redirect Trong Interceptor

```typescript
router.beforeEach((to, from) => {
  // Redirect based on condition
  if (to.path === '/old-page') {
    return '/new-page'  // Redirect
  }
})
```

---

## 4. Routing Lifecycle Hooks

### Nuxt App Hooks

```typescript
// app/plugins/hooks.ts
export default defineNuxtPlugin((nuxtApp) => {
  // Page lifecycle hooks
  nuxtApp.hook('page:start', () => {
    // Bắt đầu navigation
  })

  nuxtApp.hook('page:finish', () => {
    // Kết thúc navigation
  })

  nuxtApp.hook('page:transition:finish', () => {
    // Transition hoàn thành
  })

  // Route hooks
  nuxtApp.hook('route:error', (error) => {
    // Route error
    console.error('Route error:', error)
  })
})
```

### useRouter vs NuxtApp Hooks

```
┌─────────────────────────────────────────────────────────────────────┐
│                    HOOKS COMPARISON                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  useRouter Hooks:                                                 │
│  ─────────────────                                                 │
│  beforeEach    → Trước navigation, có thể cancel/redirect         │
│  afterEach    → Sau navigation                                    │
│  onError      → Khi có lỗi                                      │
│                                                                     │
│  NuxtApp Hooks:                                                  │
│  ──────────────                                                   │
│  page:start       → Bắt đầu render page                         │
│  page:finish      → Kết thúc render page                        │
│  page:transition:finish → Transition hoàn thành                  │
│  route:error      → Route error                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Data Fetching Lifecycle

```typescript
// useFetch/useAsyncData trong page
const { data, pending, error, refresh } = await useAsyncData(
  'key',
  () => fetchData(),
  {
    // Khi nào fetch
    server: true,    // Fetch trên server (mặc định)
    lazy: false,     // Đợi data trước khi hiển thị page

    // Retry
    retry: 3,

    // Transform data
    transform: (data) => ({ ...data })
  }
)
```

---

## 5. Error Pages

### Error Page Component

```vue
<!-- app/error.vue -->
<script setup>
defineProps({
  error: {
    type: Object,
    default: null
  }
})

const handleError = () => {
  clearError({ redirect: '/' })
}
</script>

<template>
  <div class="error-page">
    <div class="error-content">
      <h1 class="error-code">{{ error.statusCode }}</h1>
      <h2 class="error-message">{{ error.statusMessage }}</h2>
      <p>{{ error.message }}</p>
      <button @click="handleError">Quay về trang chủ</button>
    </div>
  </div>
</template>

<style scoped>
.error-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.error-code {
  font-size: 8rem;
  color: #42b883;
}

.error-message {
  font-size: 2rem;
  margin: 1rem 0;
}
</style>
```

### Custom 404 Page

```vue
<!-- app/pages/[...slug].vue -->
<script setup>
const route = useRoute()
const path = route.params.slug?.join('/') || ''
</script>

<template>
  <div class="not-found">
    <h1>404</h1>
    <h2>Trang không tồn tại</h2>
    <p>Path: /{{ path }}</p>
    <NuxtLink to="/">Quay về trang chủ</NuxtLink>
  </div>
</template>
```

### throw createError

```vue
<!-- Trong page hoặc composable -->
<script setup>
const { data } = await useFetch('/api/data')

if (!data.value) {
  throw createError({
    statusCode: 404,
    statusMessage: 'Data not found'
  })
}
</script>
```

### Error Handling Trong useFetch

```typescript
const { data, error } = await useFetch('/api/users', {
  onRequestError({ error }) {
    // Request error
    console.error('Request error:', error)
  },
  onResponseError({ response }) {
    // Response error (4xx, 5xx)
    if (response.status === 401) {
      navigateTo('/login')
    }
  }
})

if (error.value) {
  // Xử lý error
}
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ADVANCED TOPICS SUMMARY                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VALIDATION:                                                        │
│  ──────────                                                        │
│  validate: (route) => true/false/object                            │
│  return false → 404                                                │
│  return { statusCode, statusMessage } → Custom error               │
│                                                                     │
│  INTERCEPTORS:                                                      │
│  ────────────                                                      │
│  router.beforeEach()  → Cancel/redirect                            │
│  router.afterEach()   → Post-navigation                            │
│                                                                     │
│  LIFECYCLE HOOKS:                                                  │
│  ─────────────────                                                  │
│  page:start/fini sh → Page lifecycle                              │
│  route:error        → Error handling                               │
│                                                                     │
│  ERROR PAGES:                                                      │
│  ────────────                                                      │
│  app/error.vue → Global error page                                 │
│  [...slug].vue  → 404 catch-all                                    │
│  throw createError() → Trigger error                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập

### Bài 1: Route Validation Hoàn Chỉnh

Tạo page `/product/[category]/[id]` với:

1. Validate category phải thuộc danh sách
2. Validate ID phải là số hoặc UUID
3. Fetch và validate product tồn tại

### Bài 2: Navigation Interceptor

Tạo plugin với:

1. Track page views vào analytics
2. Show loading indicator khi navigation
3. Handle unsaved changes warning

### Bài 3: Custom Error Pages

Tạo error page với:

1. Hiển thị status code đẹp
2. Custom styling theo error type
3. Back to home button

---

## ▶️ Tiếp Theo

→ [Chapper 7: Exercises](07-exercises.md) - Bài tập tổng hợp và giải pháp

---

*Tài liệu thuộc phần Routing của Lộ Trình Học Nuxt 4*

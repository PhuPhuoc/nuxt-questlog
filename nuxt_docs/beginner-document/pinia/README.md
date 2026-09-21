# Pinia State Management trong Nuxt 4

Bộ tài liệu chuyên sâu về State Management với Pinia trong Nuxt 4.

## 📚 Mục lục

### Tài Liệu Chi Tiết

| File | Nội dung | Mức độ |
|------|-----------|---------|
| [01-overview.md](01-overview.md) | Tổng quan State Management | 🟢 Cơ bản |
| [02-store-structure.md](02-store-structure.md) | Cấu trúc & cách tạo Store | 🟢 Cơ bản |
| [03-ssr-hydration.md](03-ssr-hydration.md) | SSR & Hydration | 🟡 Trung bình |
| [04-advanced-techniques.md](04-advanced-techniques.md) | Kỹ thuật nâng cao | 🔴 Nâng cao |
| [05-data-flow.md](05-data-flow.md) | Sơ đồ luồng dữ liệu | 🟢 Cơ bản |
| [06-exercises.md](06-exercises.md) | Bài tập thực hành | 🔴 Nâng cao |

---

## 🚀 Quick Start

### Cài Đặt @pinia/nuxt

```bash
# Sử dụng nuxi CLI
npx nuxi@latest module add pinia
```

### Cấu Hình nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],

  pinia: {
    // Thư mục chứa stores - auto-imported
    storesDirs: ['./stores/**'],
  },
})
```

### Tạo Store Đầu Tiên

```typescript
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref<User | null>(null)
  const isLoading = ref(false)

  // Getters
  const isAuthenticated = computed(() => !!user.value)

  // Actions
  async function login(credentials: Credentials) {
    isLoading.value = true
    try {
      const response = await $fetch('/api/auth/login', {
        method: 'POST',
        body: credentials
      })
      user.value = response.user
    } finally {
      isLoading.value = false
    }
  }

  return { user, isLoading, isAuthenticated, login }
})
```

### Sử Dụng Trong Component

```vue
<!-- pages/profile.vue -->
<script setup lang="ts">
const authStore = useAuthStore() // ✅ Auto-imported!
</script>

<template>
  <div v-if="authStore.isAuthenticated">
    Welcome, {{ authStore.user?.name }}!
  </div>
</template>
```

---

## 🎯 So Sánh State Management Options

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHI NÀO DÙNG GÌ?                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ref() / reactive()                                                │
│  ├── Khi nào: State CHỈ TRONG 1 component                        │
│  ├── Ví dụ: Form state, local toggle                              │
│  └── ⚠️ Không share được giữa components                        │
│                                                                     │
│  useState()                                                        │
│  ├── Khi nào: Shared state NHỎ, đơn giản                        │
│  ├── Ví dụ: Toggle sidebar, notification queue                     │
│  └── ✅ SSR-safe, shared giữa components                         │
│                                                                     │
│  Pinia Store                                                       │
│  ├── Khi nào: State PHỨC TẠP, cần business logic                │
│  ├── Ví dụ: Auth, Cart, Products, User Preferences               │
│  └── ✅ Actions, Getters, Plugins, SSR-safe                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📋 Cheat Sheet

| Task | Solution |
|------|----------|
| Share state giữa components | Pinia Store |
| SSR-safe state | Pinia cơ bản hoặc `useCookie()` |
| Local state (1 component) | `ref()` |
| Shared state nhỏ | `useState()` |
| Persist data | `useCookie()` hoặc Plugin + localStorage |
| Destructure store | `storeToRefs()` |
| Fetch trong action | `$fetch()` |
| Fetch trong page | `useFetch()` |

---

## 🔑 Key Takeaways

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT 4 PINIA BEST PRACTICES                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Setup Store (Khuyến nghị)                                      │
│     const count = ref(0)                                          │
│     const double = computed(() => count.value * 2)                  │
│                                                                     │
│  ✅ Auto-Imports                                                   │
│     export const useXxxStore = defineStore(...)                   │
│     // Sử dụng không cần import!                                 │
│                                                                     │
│  ✅ SSR-Safe                                                       │
│     Dùng $fetch() thay vì fetch()                                │
│     Dùng useCookie() thay vì localStorage                       │
│                                                                     │
│  ✅ storeToRefs()                                                  │
│     const { user } = storeToRefs(authStore)                       │
│     // Giữ reactive khi destructuring                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🔗 Liên Kết

- [Pinia Documentation](https://pinia.vuejs.org/)
- [@pinia/nuxt Module](https://pinia.nuxtjs.org/)
- [Nuxt State Documentation](https://nuxt.com/docs/api/composables/use-state)

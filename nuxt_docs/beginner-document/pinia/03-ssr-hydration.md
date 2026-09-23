# Pinia SSR & Hydration - Server-Side Rendering

> **Mục tiêu:** Hiểu cách Pinia hoạt động với SSR trong Nuxt.

## Mục lục

1. [SSR với Pinia](#1-ssr-với-pinia)
2. [State Hydration](#2-state-hydration)
3. [Common Patterns](#3-common-patterns)

---

## 1. SSR với Pinia

### 1.1 @pinia/nuxt Module

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt']
})
```

### 1.2 Automatic Setup

@pinia/nuxt tự động:
- Tạo Pinia instance cho mỗi request
- Serialize state vào HTML
- Hydrate state trên client

---

## 2. State Hydration

### 2.1 Serialization Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SSR HYDRATION FLOW                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SERVER:                                                          │
│  ├── Create fresh Pinia instance                                 │
│  ├── Populate stores with data                                   │
│  └── Serialize state → window.__NUXT__                          │
│                                                                     │
│  CLIENT:                                                          │
│  ├── Read serialized state                                       │
│  ├── Create Pinia with pre-populated stores                     │
│  └── Hydrate (no re-fetch)                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 useState vs ref

```typescript
// ❌ SAI: Không SSR-safe
const user = ref(null)

// ✅ ĐÚNG: SSR-safe với useState
const user = useState('user', () => null)
```

---

## 3. Common Patterns

### 3.1 Auth State

```typescript
export const useAuthStore = defineStore('auth', () => {
  // useState SSR-safe
  const user = useState<User | null>('user', () => null)
  const token = useCookie('token')
  
  const isAuthenticated = computed(() => !!token.value)
  
  return { user, isAuthenticated }
})
```

---

## ▶️ Tham Khảo

→ [Pinia SSR Guide](https://pinia.vuejs.org/cookbook/composables.html#external-plugin-usage)

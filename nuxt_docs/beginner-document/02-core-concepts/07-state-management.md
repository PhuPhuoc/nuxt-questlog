# State Management - Quản Lý Trạng Thái

> **Mục tiêu:** Hiểu cách quản lý state trong Nuxt với ref, useState, và Pinia.

## Mục lục

1. [Tổng quan State Management](#1-tổng-quan-state-management)
2. [ref() - Local State](#2-ref---local-state)
3. [useState() - Shared State](#3-usestate---shared-state)
4. [Pinia Store](#4-pinia-store)
5. [Khi nào dùng gì?](#5-khi-nào-dùng-gì)

---

## 1. Tổng Quan State Management

### 1.1 Các loại State

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STATE TYPES                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. LOCAL STATE (ref)                                              │
│     ├── Chỉ trong 1 component                                      │
│     └── Ví dụ: Form input, local toggle                            │
│                                                                     │
│  2. SHARED STATE (useState)                                        │
│     ├── Chia sẻ giữa components                                   │
│     ├── SSR-safe                                                   │
│     └── Ví dụ: Theme, user preferences                             │
│                                                                     │
│  3. GLOBAL STATE (Pinia)                                           │
│     ├── Toàn app                                                   │
│     ├── Business logic                                             │
│     └── Ví dụ: Auth, Cart, Products                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 So sánh nhanh

| Loại | Phạm vi | SSR | Actions | Best cho |
|------|----------|-----|---------|----------|
| `ref()` | 1 component | ✅ | ❌ | Local UI state |
| `useState()` | App/request | ✅ | ❌ | Shared simple state |
| Pinia Store | App | ✅ | ✅ | Complex state + logic |

---

## 2. ref() - Local State

### 2.1 Cú pháp

```vue
<script setup lang="ts">
// Tạo reactive state
const count = ref(0)
const name = ref('Nam')
const isVisible = ref(true)
const user = ref({ name: 'Nam', age: 25 })
</script>
```

### 2.2 Đọc và ghi

```vue
<script setup lang="ts">
const count = ref(0)

// Đọc - trong script dùng .value
console.log(count.value)  // 0

// Ghi - trong script dùng .value
count.value = 10

// Tăng giảm
count.value++
count.value--
</script>

<template>
  <!-- Trong template KHÔNG cần .value -->
  <p>{{ count }}</p>
</template>
```

### 2.3 Ví dụ: Local Counter

```vue
<!-- components/Counter.vue -->
<script setup lang="ts">
const count = ref(0)

const doubled = computed(() => count.value * 2)

function increment() {
  count.value++
}

function decrement() {
  count.value--
}

function reset() {
  count.value = 0
}
</script>

<template>
  <div class="counter">
    <p>Count: {{ count }}</p>
    <p>Doubled: {{ doubled }}</p>
    <button @click="decrement">-</button>
    <button @click="reset">Reset</button>
    <button @click="increment">+</button>
  </div>
</template>
```

---

## 3. useState() - Shared State

### 3.1 Cú pháp

```typescript
// Tạo shared state
const userName = useState('userName', () => 'Nam')

// Đọc và ghi như ref
userName.value = 'Minh'
console.log(userName.value)
```

### 3.2 So sánh với ref

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ref() vs useState()                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ref()                                                           │
│  ├── Component-scoped                                             │
│  ├── Mỗi component có bản copy riêng                              │
│  └── State mất khi component unmount                              │
│                                                                     │
│  useState()                                                       │
│  ├── Request-scoped (SSR)                                         │
│  ├── Shared giữa components trong cùng request                    │
│  ├── Persistent across renders                                     │
│  └── SSR-safe (serialize/deserialize)                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.3 Ví dụ: Theme

```typescript
// composables/useTheme.ts
export const useTheme = () => {
  // Shared state - cùng theme cho cả app
  const theme = useState<'light' | 'dark'>('theme', () => 'light')
  
  const isDark = computed(() => theme.value === 'dark')
  
  function toggleTheme() {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
  }
  
  return {
    theme: readonly(theme),
    isDark,
    toggleTheme
  }
}
```

```vue
<!-- components/ThemeToggle.vue -->
<script setup lang="ts">
const { theme, toggleTheme } = useTheme()
</script>

<template>
  <button @click="toggleTheme">
    {{ theme === 'light' ? '🌙' : '☀️' }}
  </button>
</template>
```

```vue
<!-- components/Sidebar.vue -->
<script setup lang="ts">
const { theme, isDark } = useTheme()
</script>

<template>
  <aside :class="{ dark: isDark }">
    <!-- Sidebar content - dùng cùng theme -->
  </aside>
</template>
```

### 3.4 SSR Safety

```typescript
// useState tự động serialize state
// Server: Serialize theme → HTML
// Client: Deserialize → Hydrate

// Điều này ngăn state pollution giữa users!
```

---

## 4. Pinia Store

### 4.1 Setup Store (Khuyến nghị)

```typescript
// stores/counter.ts
export const useCounterStore = defineStore('counter', () => {
  // ===== STATE =====
  const count = ref(0)
  const history = ref<number[]>([])
  
  // ===== GETTERS =====
  const doubled = computed(() => count.value * 2)
  const average = computed(() => {
    if (history.value.length === 0) return 0
    return history.value.reduce((a, b) => a + b, 0) / history.value.length
  })
  
  // ===== ACTIONS =====
  function increment() {
    count.value++
    history.value.push(count.value)
  }
  
  function decrement() {
    count.value--
    history.value.push(count.value)
  }
  
  function reset() {
    count.value = 0
  }
  
  return {
    // State
    count,
    history,
    // Getters
    doubled,
    average,
    // Actions
    increment,
    decrement,
    reset
  }
})
```

### 4.2 Sử dụng Store

```vue
<script setup lang="ts">
// Auto-imported - không cần import!
const counterStore = useCounterStore()
</script>

<template>
  <div>
    <p>Count: {{ counterStore.count }}</p>
    <p>Doubled: {{ counterStore.doubled }}</p>
    <p>Average: {{ counterStore.average }}</p>
    
    <button @click="counterStore.increment">+</button>
    <button @click="counterStore.decrement">-</button>
    <button @click="counterStore.reset">Reset</button>
  </div>
</template>
```

### 4.3 storeToRefs

```vue
<script setup lang="ts">
import { storeToRefs } from 'pinia'

const counterStore = useCounterStore()

// ⚠️ SAI: Mất reactivity
// const { count } = counterStore

// ✅ ĐÚNG: Giữ reactivity
const { count, doubled } = storeToRefs(counterStore)

// Actions không cần storeToRefs
const { increment, decrement, reset } = counterStore
</script>

<template>
  <div>
    <!-- count là ref, tự unwrap trong template -->
    <p>Count: {{ count }}</p>
    
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="reset">Reset</button>
  </div>
</template>
```

### 4.4 Auth Store Example

```typescript
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref<User | null>(null)
  const token = useCookie('auth_token')
  
  // Getters
  const isAuthenticated = computed(() => !!token.value && !!user.value)
  const isAdmin = computed(() => user.value?.role === 'admin')
  const userName = computed(() => user.value?.name || 'Guest')
  
  // Actions
  async function login(credentials: LoginCredentials) {
    try {
      const response = await $fetch<{ user: User; token: string }>('/api/auth/login', {
        method: 'POST',
        body: credentials
      })
      
      token.value = response.token
      user.value = response.user
      
      return response
    } catch (error) {
      console.error('Login failed:', error)
      throw error
    }
  }
  
  function logout() {
    user.value = null
    token.value = null
    navigateTo('/login')
  }
  
  async function fetchUser() {
    if (!token.value) return
    
    try {
      user.value = await $fetch<User>('/api/auth/me', {
        headers: {
          Authorization: `Bearer ${token.value}`
        }
      })
    } catch {
      logout()
    }
  }
  
  return {
    user: readonly(user),
    token,
    isAuthenticated,
    isAdmin,
    userName,
    login,
    logout,
    fetchUser
  }
})
```

---

## 5. Khi Nào Dùng Gì?

### 5.1 Decision Tree

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DECISION TREE                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  State chỉ trong 1 component?                                       │
│         ↓                                                         │
│      CÓ → ref()                                                   │
│         ↓                                                         │
│      KHÔNG                                                        │
│         ↓                                                         │
│  State nhỏ, đơn giản?                                             │
│         ↓                                                         │
│      CÓ → useState()                                              │
│         ↓                                                         │
│      KHÔNG                                                        │
│         ↓                                                         │
│  State phức tạp, cần actions/logic?                               │
│         ↓                                                         │
│      CÓ → Pinia Store                                             │
│         ↓                                                         │
│      KHÔNG                                                        │
│         ↓                                                         │
│  useState()                                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Examples

| Use Case | Solution |
|----------|----------|
| Form input state | `ref()` |
| Modal visibility | `ref()` |
| Theme preference | `useState()` |
| Sidebar collapsed state | `useState()` |
| User authentication | Pinia Store |
| Shopping cart | Pinia Store |
| Products list + filters | Pinia Store |
| API data | `useFetch()` |

### 5.3 Cheat Sheet

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STATE MANAGEMENT CHEAT SHEET                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  LOCAL STATE:                                                      │
│  const count = ref(0)                                             │
│                                                                     │
│  SHARED STATE:                                                     │
│  const theme = useState('theme', () => 'light')                   │
│                                                                     │
│  PINIA STORE:                                                      │
│  // stores/counter.ts                                              │
│  export const useCounterStore = defineStore('counter', () => {     │
│    const count = ref(0)                                            │
│    return { count }                                                │
│  })                                                                │
│                                                                     │
│  // Sử dụng                                                       │
│  const store = useCounterStore()                                   │
│  store.count++                                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [03-debugging/01-common-errors.md](../03-debugging/01-common-errors.md) - Lỗi thường gặp

hoặc → [pinia/01-overview.md](../pinia/01-overview.md) - Pinia chi tiết

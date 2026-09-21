# 2. Cấu trúc & Cách tạo Store trong Nuxt 3 (Pinia Auto-Imports)

## 2.1. Cấu hình @pinia/nuxt trong nuxt.config.ts

### Cài đặt Module

```bash
# Nuxt tự động cài @pinia/nuxt khi thêm vào modules
# Hoặc cài thủ công:
npx nuxi@latest module add pinia
```

### Cấu hình nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],

  // Cấu hình Pinia (tùy chọn)
  pinia: {
    // Thư mục chứa stores - auto-imported
    storesDirs: ['./stores/**'],

    // Disable auto-imports nếu muốn import thủ công
    autoImport: {
      autoImport: false,
      injectAutoState: false
    }
  }
})
```

### Cấu trúc thư mục khuyến nghị

```
├── app/
│   ├── stores/                    # 📁 Pinia Stores (auto-imported)
│   │   ├── auth.ts               # Auth store
│   │   ├── cart.ts               # Cart store
│   │   └── product.ts             # Product store
│   ├── composables/              # Composables (nếu dùng useState)
│   │   └── useNotifications.ts
│   ├── components/
│   └── pages/
├── server/
│   └── api/
├── nuxt.config.ts
└── package.json
```

---

## 2.2. Hai Style viết Store

### Option Store Style

```typescript
// stores/counter.options.ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  // ===== STATE =====
  state: () => ({
    count: 0,
    userName: 'Guest',
    lastUpdated: null as Date | null,
    history: [] as number[]
  }),

  // ===== GETTERS (computed properties) =====
  getters: {
    // getter đơn giản - trả về state
    doubleCount: (state) => state.count * 2,

    // getter có thể truy cập other getters bằng `this`
    doubleCountPlusOne(): number {
      return this.doubleCount + 1
    },

    // getter với tham số (factory pattern)
    getHistoryItem: (state) => {
      return (index: number) => state.history[index] ?? null
    },

    // getter kiểm tra điều kiện
    isPositive: (state) => state.count > 0,
    isEmpty: (state) => state.count === 0
  },

  // ===== ACTIONS =====
  actions: {
    // Action đồng bộ
    increment() {
      this.count++
      this.lastUpdated = new Date()
      this.history.push(this.count)
    },

    decrement() {
      this.count--
      this.lastUpdated = new Date()
    },

    // Action bất đồng bộ
    async fetchInitialCount() {
      try {
        const response = await $fetch<{ count: number }>('/api/counter')
        this.count = response.count
        this.lastUpdated = new Date()
      } catch (error) {
        console.error('Failed to fetch count:', error)
        throw error
      }
    },

    // Action với parameter
    setCount(newCount: number) {
      if (newCount < 0) {
        throw new Error('Count cannot be negative')
      }
      this.count = newCount
      this.history.push(newCount)
    },

    // Action reset
    reset() {
      this.$reset() // ⚠️ Chỉ có trong Option Store
    }
  }
})
```

### Setup Store Style (Composition API)

```typescript
// stores/counter.setup.ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  // ===== STATE =====
  const count = ref(0)
  const userName = ref('Guest')
  const lastUpdated = ref<Date | null>(null)
  const history = ref<number[]>([])

  // ===== GETTERS =====
  const doubleCount = computed(() => count.value * 2)
  const doubleCountPlusOne = computed(() => doubleCount.value + 1)

  // Getter với tham số
  const getHistoryItem = computed(() => {
    return (index: number) => history.value[index] ?? null
  })

  const isPositive = computed(() => count.value > 0)
  const isEmpty = computed(() => count.value === 0)

  // ===== ACTIONS =====
  function increment() {
    count.value++
    lastUpdated.value = new Date()
    history.value.push(count.value)
  }

  function decrement() {
    count.value--
    lastUpdated.value = new Date()
  }

  async function fetchInitialCount() {
    try {
      const response = await $fetch<{ count: number }>('/api/counter')
      count.value = response.count
      lastUpdated.value = new Date()
    } catch (error) {
      console.error('Failed to fetch count:', error)
      throw error
    }
  }

  function setCount(newCount: number) {
    if (newCount < 0) {
      throw new Error('Count cannot be negative')
    }
    count.value = newCount
    history.value.push(newCount)
  }

  function reset() {
    // Manual reset for setup store
    count.value = 0
    userName.value = 'Guest'
    lastUpdated.value = null
    history.value = []
  }

  // ⚠️ BẮT BUỘC: Phải return tất cả refs, computed, functions
  return {
    // State
    count,
    userName,
    lastUpdated,
    history,
    // Getters
    doubleCount,
    doubleCountPlusOne,
    getHistoryItem,
    isPositive,
    isEmpty,
    // Actions
    increment,
    decrement,
    fetchInitialCount,
    setCount,
    reset
  }
})
```

### So sánh Option Store vs Setup Store

| Tiêu chí | Option Store | Setup Store |
|----------|--------------|-------------|
| Cú pháp | Object-based | Composition API |
| `this` context | Có (`this.count`) | Không (dùng biến) |
| `$reset()` | Có sẵn | Phải tự viết |
| TypeScript | Cần khai báo kiểu | Inference tự động |
| Reusability | Hạn chế | Tốt (có thể extract logic) |
| Mixins | Hỗ trợ | Không hỗ trợ |
| Khuyến nghị | Đơn giản, nhanh | Phức tạp, TypeScript |

### Setup Store - Best Practice cho TypeScript

```typescript
// stores/user.ts - Ví dụ thực tế
import { defineStore } from 'pinia'
import type { User, UserPreferences } from '~/types'

interface UserState {
  user: User | null
  preferences: UserPreferences
  isLoading: boolean
  error: string | null
}

export const useUserStore = defineStore('user', () => {
  // ===== STATE =====
  const user = ref<User | null>(null)
  const preferences = ref<UserPreferences>({
    theme: 'light',
    language: 'en',
    notifications: true
  })
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  // ===== GETTERS =====
  const isAuthenticated = computed(() => !!user.value)
  const userName = computed(() => user.value?.name ?? 'Guest')
  const userInitials = computed(() => {
    if (!user.value?.name) return 'G'
    return user.value.name
      .split(' ')
      .map(n => n[0])
      .join('')
      .toUpperCase()
      .slice(0, 2)
  })

  // ===== ACTIONS =====
  async function login(credentials: { email: string; password: string }) {
    isLoading.value = true
    error.value = null

    try {
      const response = await $fetch<{ user: User; token: string }>('/api/auth/login', {
        method: 'POST',
        body: credentials
      })
      user.value = response.user
      return response
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Login failed'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  async function fetchCurrentUser() {
    if (user.value) return // Đã có user

    isLoading.value = true
    error.value = null

    try {
      user.value = await $fetch<User>('/api/auth/me')
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to fetch user'
    } finally {
      isLoading.value = false
    }
  }

  function logout() {
    user.value = null
  }

  function updatePreferences(updates: Partial<UserPreferences>) {
    preferences.value = { ...preferences.value, ...updates }
  }

  return {
    // State (readonly refs để prevent direct mutation)
    user: readonly(user),
    preferences: readonly(preferences),
    isLoading: readonly(isLoading),
    error: readonly(error),
    // Getters
    isAuthenticated,
    userName,
    userInitials,
    // Actions
    login,
    fetchCurrentUser,
    logout,
    updatePreferences
  }
})
```

---

## 2.3. Auto-Imports Store trong Nuxt

### Tại sao không cần import?

```
┌──────────────────────────────────────────────────────────────────┐
│                    NUXT AUTO-IMPORT SYSTEM                       │
│                                                                   │
│  Khi Nuxt khởi động, nó quét các thư mục:                       │
│  ├── app/composables/     → Auto-import tất cả exports         │
│  ├── app/utils/           → Auto-import tất cả exports         │
│  └── app/stores/          → Auto-import tất cả exports (Pinia) │
│                                                                   │
│  Quy tắc đặt tên:                                                │
│  export const useXxxStore = defineStore(...)                    │
│           ↑                                                       │
│  ├── Phải bắt đầu bằng "use"                                    │
│  └── Phải kết thúc bằng "Store"                                 │
│                                                                   │
│  Result: Bạn có thể gọi useXxxStore() ở BẤT KỲ ĐÂU             │
│          mà không cần import!                                    │
└──────────────────────────────────────────────────────────────────┘
```

### Ví dụ Auto-Imports

```typescript
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const isLoggedIn = ref(false)
  return { isLoggedIn }
})
```

```vue
<!-- pages/dashboard.vue -->
<script setup lang="ts">
// ✅ Không cần import! Nuxt tự động tìm useAuthStore
const authStore = useAuthStore()

// Hoặc destructuring với storeToRefs để giữ reactivity
const { isLoggedIn } = storeToRefs(authStore)
</script>
```

```vue
<!-- components/Sidebar.vue -->
<script setup lang="ts">
// ✅ Cũng không cần import!
const { isLoggedIn, user } = storeToRefs(useAuthStore())
const { logout } = useAuthStore()
</script>
```

### Quy tắc đặt tên Store

```typescript
// ✅ ĐÚNG - Auto-import hoạt động
export const useAuthStore = defineStore('auth', ...)
export const useCartStore = defineStore('cart', ...)
export const useProductStore = defineStore('product', ...)

// ❌ SAI - Auto-import KHÔNG hoạt động
export const authStore = defineStore('auth', ...)           // Thiếu "use"
export const useAuth = defineStore('auth', ...)             // Thiếu "Store"
export const useAuthStoreForNuxt = defineStore('auth', ...) // Quá dài
```

### Cấu hình Auto-Import tùy chỉnh

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],

  pinia: {
    // Thư mục tìm kiếm stores (glob pattern)
    storesDirs: [
      './stores/**',      // Default
      './app/stores/**',  // Thêm thư mục khác
    ],

    // Tùy chỉnh auto-import
    autoImport: {
      // Import these functions from 'pinia' instead of auto-importing
      imports: ['defineStore', 'storeToRefs'],

      // Disable auto-import hoàn toàn
      // autoImport: false,
    }
  }
})
```

### Cách hoạt động (Low-level)

```typescript
// Khi Nuxt build, nó tạo file .nuxt/auto-imports.ts:
/*
┌─────────────────────────────────────────────────────────────┐
│                  .nuxt/auto-imports.d.ts                     │
├─────────────────────────────────────────────────────────────┤
│  // Auto-generated - KHÔNG SỬA TAY                           │
│  declare function useAuthStore(...): ReturnType<...>       │
│  declare function useCartStore(...): ReturnType<...>       │
│  declare function useProductStore(...): ReturnType<...>    │
│                                                              │
│  declare const storeToRefs: typeof import('pinia')['storeToRefs']
└─────────────────────────────────────────────────────────────┘
*/

// Bạn chỉ cần viết:
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => { ... })

// Nuxt tự động generate type declaration và import
// → Bạn không cần import thủ công!
```

---

## 2.4. Sử dụng Store trong Component

### Basic Usage

```vue
<!-- components/Counter.vue -->
<script setup lang="ts">
// Lấy store instance
const counterStore = useCounterStore()

// Gọi action
const handleIncrement = () => {
  counterStore.increment()
}

// Truy cập state trực tiếp
const currentCount = counterStore.count
</script>

<template>
  <div class="counter">
    <h2>Count: {{ currentCount }}</h2>
    <h3>Double: {{ counterStore.doubleCount }}</h3>
    <button @click="handleIncrement">+</button>
  </div>
</template>
```

### Với storeToRefs (Giữ reactivity khi destructuring)

```vue
<!-- components/UserProfile.vue -->
<script setup lang="ts">
const authStore = useAuthStore()

// ❌ SAI: Mất reactivity
const { user, isLoggedIn } = authStore
// user và isLoggedIn không còn reactive!

// ✅ ĐÚNG: Dùng storeToRefs
const { user, isLoggedIn } = storeToRefs(authStore)

// ✅ HOẶC: Chỉ đọc, không cần storeToRefs
const user = computed(() => authStore.user)
const isLoggedIn = computed(() => authStore.isAuthenticated)

// Actions không cần storeToRefs
const { login, logout } = authStore
</script>

<template>
  <div v-if="isLoggedIn">
    <p>Welcome, {{ user?.name }}</p>
    <button @click="logout">Logout</button>
  </div>
  <div v-else>
    <button @click="login({ email: 'test@test.com', password: '123' })">
      Login
    </button>
  </div>
</template>
```

### Computed Properties từ Store

```vue
<script setup lang="ts">
const cartStore = useCartStore()

// Tạo computed từ store getters
const canCheckout = computed(() => {
  return cartStore.itemCount > 0 && !cartStore.isProcessing
})

const cartSummary = computed(() => ({
  items: cartStore.itemCount,
  total: cartStore.totalPrice,
  formattedTotal: cartStore.formattedTotal
}))
</script>

<template>
  <button :disabled="!canCheckout">
    Checkout ({{ cartSummary.formattedTotal }})
  </button>
</template>
```

---

## 2.5. Tóm tắt

```
┌──────────────────────────────────────────────────────────────────┐
│                    STORE PATTERNS SUMMARY                         │
│                                                                   │
│  1. Setup Store (Khuyến nghị)                                    │
│     ├── Dùng ref()/computed() thay vì state/getters              │
│     ├── Tất cả phải return              │
│     ├── Type inference tốt hơn                                  │
│     └── Tái sử dụng logic dễ hơn                                │
│                                                                   │
│  2. Auto-Imports                                                  │
│     ├── Đặt tên: useXxxStore                                    │
│     ├── Đặt trong: stores/ hoặc app/stores/                    │
│     └── KHÔNG cần import trong component                        │
│                                                                   │
│  3. storeToRefs()                                                │
│     ├── Dùng khi destructuring state/getters                    │
│     └── Actions không cần storeToRefs                            │
│                                                                   │
│  4. Readonly State                                               │
│     ├── Dùng readonly() cho state trong return                  │
│     └── Buộc mutation qua actions                                │
└──────────────────────────────────────────────────────────────────┘
```

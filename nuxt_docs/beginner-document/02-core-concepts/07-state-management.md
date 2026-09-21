# State Management

> **Mục tiêu:** Hiểu cách quản lý state trong Nuxt với ref, useState, và Pinia.

## Mục lục

1. [State là gì?](#1-state-là-gì)
2. [ref() - Local State](#2-ref---local-state)
3. [useState() - Shared State](#3-usestate---shared-state)
4. [Pinia - Global Store](#4-pinia---global-store)
5. [Khi nào dùng cái nào?](#5-khi-nào-dùng-cái-nào)

---

## 1. State là gì?

### Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STATE LÀ GÌ?                                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  State = Dữ liệu mà app LƯU TRỮ và QUẢN LÝ                     │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── User đang đăng nhập                                         │
│  ├── Giỏ hàng có bao nhiêu items                                │
│  ├── Form đang nhập dữ liệu gì                                  │
│  ├── Toggle sidebar đang mở hay đóng                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Các loại State trong Nuxt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CÁC LOẠI STATE TRONG NUXT                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  LOCAL STATE (ref/reactive)                                        │
│  ├── Chỉ trong 1 component                                       │
│  ├── Không chia sẻ                                               │
│  └── Ví dụ: Form input, local toggle                            │
│                                                                     │
│  SHARED STATE (useState)                                          │
│  ├── Chia sẻ giữa components                                    │
│  ├── SSR-safe                                                   │
│  └── Ví dụ: Sidebar toggle, theme                               │
│                                                                     │
│  GLOBAL STATE (Pinia)                                             │
│  ├── Chia sẻ toàn app                                           │
│  ├── Có business logic                                          │
│  └── Ví dụ: Auth, Cart, User preferences                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. ref() - Local State

### 2.1 Khi nào dùng?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHI NÀO DÙNG ref()?                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Dùng ref() khi:                                               │
│  ├── State CHỈ trong 1 component                                  │
│  ├── State ĐƠN GIẢN (primitive values)                          │
│  ├── State TẠM THỜI (không cần chia sẻ)                        │
│  └── Ví dụ:                                                       │
│      ├── Form input values                                       │
│      ├── Local toggle state                                      │
│      ├── Animation state                                         │
│      └── Temporary loading state                                  │
│                                                                     │
│  ❌ KHÔNG dùng ref() khi:                                        │
│  ├── State cần chia sẻ giữa components                         │
│  ├── State cần persist (lưu lại khi reload)                    │
│  └── State phức tạp với business logic                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 Ví dụ: Form State

```vue
<script setup>
// Local state - chỉ dùng trong component này
const form = ref({
  name: '',
  email: '',
  message: ''
})

const isSubmitting = ref(false)
const errors = ref({})

async function handleSubmit() {
  // Validate
  errors.value = {}

  if (!form.value.name) {
    errors.value.name = 'Name is required'
  }

  if (!form.value.email) {
    errors.value.email = 'Email is required'
  }

  if (Object.keys(errors.value).length > 0) {
    return
  }

  // Submit
  isSubmitting.value = true
  try {
    await $fetch('/api/contact', {
      method: 'POST',
      body: form.value
    })
    alert('Gửi thành công!')
  } finally {
    isSubmitting.value = false
  }
}
</script>
```

### 2.3 Ví dụ: Local Toggle

```vue
<script setup>
const isSidebarOpen = ref(false)
const isModalOpen = ref(false)

function toggleSidebar() {
  isSidebarOpen.value = !isSidebarOpen.value
}
</script>
```

---

## 3. useState() - Shared State

### 3.1 Khi nào dùng?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHI NÀO DÙNG useState()?                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Dùng useState() khi:                                          │
│  ├── State CẦN CHIA SẺ giữa components                           │
│  ├── State ĐƠN GIẢN (không cần business logic)                    │
│  ├── State CẦN SSR-safe (shared across server/client)             │
│  └── Ví dụ:                                                       │
│      ├── Theme (light/dark)                                      │
│      ├── Sidebar open/close                                      │
│      ├── Notification queue                                      │
│      └── User preferences (simple)                               │
│                                                                     │
│  ❌ KHÔNG dùng useState() khi:                                   │
│  ├── State cần complex logic/actions                             │
│  ├── State cần persist lâu dài                                  │
│  └── State cần devtools debugging                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Cú pháp cơ bản

```typescript
// Tạo state
const count = useState('count', () => 0)

// Đọc state
console.log(count.value) // 0

// Ghi state
count.value = 10

// Computed state
const doubleCount = useState('doubleCount', () => count.value * 2)
```

### 3.3 Ví dụ: Theme Toggle

```typescript
// composables/useTheme.ts
export const useTheme = () => {
  // State được share giữa tất cả components
  const theme = useState('theme', () => 'light')

  const isDark = computed(() => theme.value === 'dark')

  function toggleTheme() {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
  }

  return {
    theme,
    isDark,
    toggleTheme
  }
}
```

```vue
<!-- Header.vue -->
<script setup>
const { theme, isDark, toggleTheme } = useTheme()
</script>

<template>
  <header :class="{ dark: isDark }">
    <button @click="toggleTheme">
      {{ isDark ? '🌙' : '☀️' }}
    </button>
  </header>
</template>
```

```vue
<!-- Sidebar.vue -->
<script setup>
// Cùng theme state!
const { theme, isDark } = useTheme()
</script>

<template>
  <aside :class="{ dark: isDark }">
    Sidebar content
  </aside>
</template>
```

### 3.4 SSR Safety

```typescript
// useState() tự động:
// 1. Serialize state từ server
// 2. Deserialize state trên client
// 3. Tránh pollution giữa các requests

// Ví dụ: User cart
const cart = useState('cart', () => ({
  items: [],
  total: 0
}))

// Server: Mỗi user có cart riêng
// Client: Hydrate từ server state
```

---

## 4. Pinia - Global Store

### 4.1 Khi nào dùng?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHI NÀO DÙNG PINIA?                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Dùng Pinia khi:                                               │
│  ├── State PHỨC TẠP với business logic                           │
│  ├── State CẦN persist (lưu localStorage)                        │
│  ├── State CẦN actions (async operations)                        │
│  ├── State CẦN devtools debugging                                │
│  ├── State TypeScript-heavy                                       │
│  └── Ví dụ:                                                       │
│      ├── Auth state (login, logout, token)                        │
│      ├── Cart state (add, remove, checkout)                       │
│      ├── User preferences (complex)                               │
│      └── Products catalog (filter, sort, pagination)             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 Tạo Store

```typescript
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref<User | null>(null)
  const token = useCookie('auth_token')

  // Getters
  const isLoggedIn = computed(() => !!token.value && !!user.value)
  const isAdmin = computed(() => user.value?.role === 'admin')
  const userName = computed(() => user.value?.name || 'Guest')

  // Actions
  async function login(email: string, password: string) {
    try {
      const response = await $fetch('/api/auth/login', {
        method: 'POST',
        body: { email, password }
      })

      token.value = response.token
      user.value = response.user

      return { success: true }
    } catch (error) {
      return { success: false, error }
    }
  }

  async function logout() {
    token.value = null
    user.value = null
    await navigateTo('/login')
  }

  async function fetchUser() {
    if (!token.value) return

    try {
      user.value = await $fetch('/api/auth/me', {
        headers: { Authorization: `Bearer ${token.value}` }
      })
    } catch {
      logout()
    }
  }

  return {
    // State
    user,
    token,
    // Getters
    isLoggedIn,
    isAdmin,
    userName,
    // Actions
    login,
    logout,
    fetchUser
  }
})
```

### 4.3 Sử dụng Store

```vue
<script setup>
// Auto-imported - không cần import!
const authStore = useAuthStore()

// Đọc state
console.log(authStore.userName)

// Đọc getter
if (authStore.isLoggedIn) {
  console.log('Welcome!')
}

// Gọi action
async function handleLogin() {
  const result = await authStore.login(email, password)
  if (result.success) {
    navigateTo('/dashboard')
  }
}
</script>
```

### 4.4 Destructure với storeToRefs

```vue
<script setup>
const authStore = useAuthStore()

// ❌ SAI - Mất reactivity!
const { user, isLoggedIn } = authStore

// ✅ ĐÚNG - Giữ reactivity
import { storeToRefs } from 'pinia'
const { user, isLoggedIn } = storeToRefs(authStore)

// Actions không cần storeToRefs
const { login, logout } = authStore
</script>
```

### 4.5 Cart Store Example

```typescript
// app/stores/cart.ts
export const useCartStore = defineStore('cart', () => {
  // State - persist với useCookie
  const items = ref<CartItem[]>([])
  const shippingFee = ref(30000)

  // Getters
  const itemCount = computed(() => items.value.length)
  const subtotal = computed(() =>
    items.value.reduce((sum, item) => sum + item.price * item.quantity, 0)
  )
  const total = computed(() => subtotal.value + shippingFee.value)

  // Actions
  function addItem(product: Product, quantity = 1) {
    const existing = items.value.find(item => item.id === product.id)

    if (existing) {
      existing.quantity += quantity
    } else {
      items.value.push({
        id: product.id,
        name: product.name,
        price: product.price,
        image: product.image,
        quantity
      })
    }
  }

  function removeItem(productId: number) {
    items.value = items.value.filter(item => item.id !== productId)
  }

  function updateQuantity(productId: number, quantity: number) {
    const item = items.value.find(item => item.id === productId)
    if (item) {
      item.quantity = Math.max(0, quantity)
      if (item.quantity === 0) {
        removeItem(productId)
      }
    }
  }

  function clearCart() {
    items.value = []
  }

  return {
    items,
    itemCount,
    subtotal,
    total,
    addItem,
    removeItem,
    updateQuantity,
    clearCart
  }
})
```

---

## 5. Khi Nào Dùng Cái Nào?

### Decision Tree

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STATE MANAGEMENT DECISION TREE                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  State cần chia sẻ GIỮA CÁC components?                      │
│         ↓                                                           │
│    KHÔNG                                                         │
│         ↓                                                           │
│  Dùng ref() - Local state                                      │
│                                                                     │
│  ──────────────────────────────────────────────────────────────── │
│                                                                     │
│  State cần chia sẻ GIỮA CÁC components?                      │
│         ↓                                                           │
│    CÓ                                                             │
│         ↓                                                           │
│  State PHỨC TẠP với business logic?                           │
│         ↓                                                           │
│    CÓ → Dùng Pinia Store                                     │
│         ↓                                                           │
│    KHÔNG                                                        │
│         ↓                                                           │
│  State ĐƠN GIẢN, không cần persist?                         │
│         ↓                                                           │
│    CÓ → Dùng useState()                                     │
│         ↓                                                           │
│    KHÔNG → Dùng Pinia Store (cần persist)               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh nhanh

| Loại | Khi nào | Ví dụ |
|------|---------|-------|
| `ref()` | Local, đơn giản | Form input, local toggle |
| `useState()` | Shared, đơn giản | Theme, sidebar |
| `Pinia` | Complex, logic | Auth, Cart, Products |

### Examples theo use case

```vue
<!-- 1. Local state - ref() -->
<script setup>
const searchQuery = ref('')
const isLoading = ref(false)
</script>

<!-- 2. Shared simple state - useState() -->
<script setup>
// composables/useUI.ts
const sidebarOpen = useState('sidebar', () => false)
const notifications = useState('notifications', () => [])
</script>

<!-- 3. Complex state - Pinia -->
<script setup>
// stores/cart.ts
const cart = useCartStore()
const auth = useAuthStore()
const products = useProductStore()
</script>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STATE MANAGEMENT CHEAT SHEET                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ref() - Local State:                                            │
│  ├── const count = ref(0)                                        │
│  ├── Chỉ trong 1 component                                       │
│  └── Dùng cho: form inputs, local toggles                         │
│                                                                     │
│  useState() - Shared State:                                       │
│  ├── const theme = useState('theme', () => 'light')               │
│  ├── Chia sẻ giữa components                                     │
│  └── Dùng cho: theme, sidebar, notifications                     │
│                                                                     │
│  Pinia - Global Store:                                            │
│  ├── export const useAuthStore = defineStore('auth', ...)         │
│  ├── State + Getters + Actions                                   │
│  └── Dùng cho: auth, cart, products                             │
│                                                                     │
│  storeToRefs():                                                   │
│  ├── const { user } = storeToRefs(authStore)                     │
│  └── Giữ reactivity khi destructure                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [route/](../route/) - Routing nâng cao (chi tiết hơn)

hoặc → [pinia/](../pinia/) - Pinia chi tiết (chi tiết hơn)

hoặc → [../00-prerequisites/01-vue3-composition-api.md](../00-prerequisites/01-vue3-composition-api.md) - Ôn lại Vue Composition API

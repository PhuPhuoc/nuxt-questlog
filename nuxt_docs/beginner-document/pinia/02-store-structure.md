# Pinia Store - Cấu Trúc Chi Tiết

> **Mục tiêu:** Học cách tạo và tổ chức Pinia stores trong Nuxt.

## Mục lục

1. [Setup Store Pattern](#1-setup-store-pattern)
2. [Option Store Pattern](#2-option-store-pattern)
3. [Getters](#3-getters)
4. [Actions](#4-actions)
5. [Store với Plugins](#5-store-với-plugins)

---

## 1. Setup Store Pattern (Khuyến nghị)

### 1.1 Cú pháp

```typescript
// stores/counter.ts
export const useCounterStore = defineStore('counter', () => {
  // State - như refs
  const count = ref(0)
  const name = ref('Counter')

  // Getters - như computed
  const doubled = computed(() => count.value * 2)
  const isPositive = computed(() => count.value > 0)

  // Actions - functions
  function increment() {
    count.value++
  }

  function decrement() {
    count.value--
  }

  function reset() {
    count.value = 0
  }

  function setCount(value: number) {
    count.value = value
  }

  // Return để expose ra
  return {
    // State (read-write)
    count,
    name,
    // Getters (read-only)
    doubled,
    isPositive,
    // Actions
    increment,
    decrement,
    reset,
    setCount
  }
})
```

### 1.2 Sử dụng

```vue
<script setup lang="ts">
// Auto-imported - không cần import!
const counterStore = useCounterStore()

// Đọc state
console.log(counterStore.count)
console.log(counterStore.doubled)

// Gọi actions
counterStore.increment()
counterStore.setCount(10)
</script>
```

---

## 2. Option Store Pattern

### 2.1 Cú pháp

```typescript
// stores/counter-options.ts
export const useCounterOptionsStore = defineStore('counter', {
  // State
  state: () => ({
    count: 0,
    name: 'Counter'
  }),

  // Getters
  getters: {
    doubled: (state) => state.count * 2,
    isPositive: (state) => state.count > 0
  },

  // Actions
  actions: {
    increment() {
      this.count++
    },
    decrement() {
      this.count--
    },
    reset() {
      this.count = 0
    }
  }
})
```

### 2.2 So sánh Setup vs Options

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SETUP vs OPTIONS STORE                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SETUP STORE:                                                     │
│  ├── Khuyến nghị cho code mới                                    │
│  ├── TypeScript-friendly                                           │
│  ├── Gần với <script setup>                                      │
│  └── Dùng ref/computed trực tiếp                                 │
│                                                                     │
│  OPTIONS STORE:                                                    │
│  ├── Legacy support                                               │
│  ├── Vuex-like                                                   │
│  ├── Dùng this.xxx                                               │
│  └── Quen thuộc với Vuex users                                   │
│                                                                     │
│  → Khuyến nghị: Dùng SETUP STORE                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Getters

### 3.1 Read-only Getters

```typescript
export const useProductStore = defineStore('products', () => {
  const products = ref<Product[]>([])

  // Getter: Filtered list
  const expensiveProducts = computed(() =>
    products.value.filter(p => p.price > 100)
  )

  // Getter: Transformed data
  const productNames = computed(() =>
    products.value.map(p => p.name)
  )

  // Getter: Aggregated value
  const totalValue = computed(() =>
    products.value.reduce((sum, p) => sum + p.price, 0)
  )

  // Getter: Find by ID
  const getProductById = computed(() => (id: number) =>
    products.value.find(p => p.id === id)
  )

  return {
    products,
    expensiveProducts,
    productNames,
    totalValue,
    getProductById
  }
})
```

### 3.2 Getters với Parameters

```typescript
// Cách 1: Computed trả về function
const productsByCategory = computed(() => (category: string) =>
  products.value.filter(p => p.category === category)
)

// Sử dụng
const electronics = productStore.productsByCategory('electronics')

// Cách 2: Regular function (không cached)
function findProduct(id: number) {
  return products.value.find(p => p.id === id)
}
```

---

## 4. Actions

### 4.1 Synchronous Actions

```typescript
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)

  function setUser(newUser: User) {
    user.value = newUser
  }

  function clearUser() {
    user.value = null
  }
})
```

### 4.2 Async Actions

```typescript
export const useUserStore = defineStore('users', () => {
  const users = ref<User[]>([])
  const loading = ref(false)
  const error = ref<string | null>(null)

  async function fetchUsers() {
    loading.value = true
    error.value = null

    try {
      users.value = await $fetch('/api/users')
    } catch (e: any) {
      error.value = e.message || 'Failed to fetch users'
    } finally {
      loading.value = false
    }
  }

  async function createUser(data: CreateUserData) {
    const newUser = await $fetch<User>('/api/users', {
      method: 'POST',
      body: data
    })
    users.value.push(newUser)
    return newUser
  }

  return {
    users,
    loading,
    error,
    fetchUsers,
    createUser
  }
})
```

### 4.3 Actions với Parameters

```typescript
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])

  function addItem(product: Product, quantity = 1) {
    const existingItem = items.value.find(i => i.productId === product.id)

    if (existingItem) {
      existingItem.quantity += quantity
    } else {
      items.value.push({
        productId: product.id,
        name: product.name,
        price: product.price,
        quantity
      })
    }
  }

  function removeItem(productId: number) {
    const index = items.value.findIndex(i => i.productId === productId)
    if (index !== -1) {
      items.value.splice(index, 1)
    }
  }

  function updateQuantity(productId: number, quantity: number) {
    const item = items.value.find(i => i.productId === productId)
    if (item) {
      if (quantity <= 0) {
        removeItem(productId)
      } else {
        item.quantity = quantity
      }
    }
  }

  return {
    items,
    addItem,
    removeItem,
    updateQuantity
  }
})
```

---

## 5. Store với Plugins

### 5.1 Persistence Plugin

```typescript
// plugins/pinia-persist.client.ts
export const piniaPlugin = definePiniaPlugin(({ store }) => {
  // Chỉ persist các stores cụ thể
  if (store.$id === 'user' || store.$id === 'cart') {
    // Restore state
    const savedState = localStorage.getItem(store.$id)
    if (savedState) {
      store.$patch(JSON.parse(savedState))
    }

    // Save state on change
    store.$subscribe((mutation, state) => {
      localStorage.setItem(store.$id, JSON.stringify(state))
    })
  }
})
```

### 5.2 Logger Plugin

```typescript
// plugins/pinia-logger.ts
export const piniaLoggerPlugin = definePiniaPlugin(({}) => {
  return ({ store }) => {
    // Log state changes
    store.$subscribe((mutation, state) => {
      console.log(`[${store.$id}]`, mutation.type, mutation.storeId)
    })

    // Log actions
    store.$onAction(({ name, args }) => {
      console.log(`[${store.$id}] Action: ${name}`, args)
    })
  }
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PINIA STORE CHEAT SHEET                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SETUP STORE (Khuyến nghị):                                        │
│  ────────────────────────────────────────                            │
│  export const useCounterStore = defineStore('counter', () => {       │
│    const count = ref(0)                                             │
│    const doubled = computed(() => count.value * 2)                  │
│                                                                     │
│    function increment() { count.value++ }                            │
│                                                                     │
│    return { count, doubled, increment }                              │
│  })                                                                 │
│                                                                     │
│  USAGE:                                                             │
│  const store = useCounterStore()                                     │
│  store.count++                                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [03-best-practices.md](03-best-practices.md) - Best Practices

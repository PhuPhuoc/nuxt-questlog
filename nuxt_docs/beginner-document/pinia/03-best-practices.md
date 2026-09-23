# Pinia Best Practices - Thực Hành Tốt

> **Mục tiêu:** Học cách tổ chức và sử dụng Pinia stores một cách hiệu quả.

## Mục lục

1. [Store Organization](#1-store-organization)
2. [Naming Conventions](#2-naming-conventions)
3. [SSR Considerations](#3-ssr-considerations)
4. [Performance Tips](#4-performance-tips)

---

## 1. Store Organization

### 1.1 Folder Structure

```
📁 app/stores/
├── 📄 auth.ts           # Auth store
├── 📄 cart.ts          # Shopping cart
├── 📄 products.ts     # Products catalog
├── 📄 ui.ts           # UI state (modals, toasts)
│
└── 📁 users/
    ├── 📄 index.ts     # Main users store
    ├── 📄 profile.ts  # User profile
    └── 📄 settings.ts # User settings
```

### 1.2 Single Store vs Multiple Stores

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SINGLE vs MULTIPLE STORES                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SINGLE STORE (Cart):                                              │
│  ├── items, addItem, removeItem, total, checkout                  │
│  └── Dùng khi: Logic liên quan, dễ quản lý                       │
│                                                                     │
│  MULTIPLE STORES (Users):                                          │
│  ├── users/index.ts - Main user data                              │
│  ├── users/profile.ts - Profile specific                          │
│  ├── users/settings.ts - Settings specific                        │
│  └── Dùng khi: Phân chia responsibility rõ ràng                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.3 Feature-based Stores

```typescript
// stores/todo.ts - Todo feature store
export const useTodoStore = defineStore('todo', () => {
  // State
  const todos = ref<Todo[]>([])
  const filter = ref<'all' | 'active' | 'completed'>('all')

  // Getters
  const filteredTodos = computed(() => {
    switch (filter.value) {
      case 'active':
        return todos.value.filter(t => !t.completed)
      case 'completed':
        return todos.value.filter(t => t.completed)
      default:
        return todos.value
    }
  })

  const activeCount = computed(() =>
    todos.value.filter(t => !t.completed).length
  )

  const completedCount = computed(() =>
    todos.value.filter(t => t.completed).length
  )

  // Actions
  function addTodo(text: string) {
    todos.value.push({
      id: Date.now(),
      text,
      completed: false
    })
  }

  function toggleTodo(id: number) {
    const todo = todos.value.find(t => t.id === id)
    if (todo) {
      todo.completed = !todo.completed
    }
  }

  function removeTodo(id: number) {
    const index = todos.value.findIndex(t => t.id === id)
    if (index !== -1) {
      todos.value.splice(index, 1)
    }
  }

  function clearCompleted() {
    todos.value = todos.value.filter(t => !t.completed)
  }

  return {
    todos,
    filter,
    filteredTodos,
    activeCount,
    completedCount,
    addTodo,
    toggleTodo,
    removeTodo,
    clearCompleted
  }
})
```

---

## 2. Naming Conventions

### 2.1 Store Functions

```typescript
// ✅ ĐÚNG: useXxxStore
export const useAuthStore = defineStore('auth', () => {})
export const useCartStore = defineStore('cart', () => {})
export const useUserStore = defineStore('user', () => {})
export const useProductStore = defineStore('product', () => {})

// ❌ SAI: Thiếu Store suffix
export const useAuth = defineStore('auth', () => {})
export const useCart = defineStore('cart', () => {})
```

### 2.2 State Properties

```typescript
export const useUserStore = defineStore('user', () => {
  // ✅ ĐÚNG: Mô tả rõ ràng
  const user = ref<User | null>(null)
  const isLoading = ref(false)
  const errorMessage = ref<string | null>(null)
  const lastFetchedAt = ref<Date | null>(null)

  // ❌ SAI: Quá ngắn, không rõ ý nghĩa
  const u = ref(null)
  const loading = ref(false)
})
```

### 2.3 Actions

```typescript
export const useProductStore = defineStore('product', () => {
  // ✅ ĐÚNG: Verb + Noun
  async function fetchProducts() {}
  async function createProduct() {}
  async function updateProduct() {}
  async function deleteProduct() {}

  // ❌ SAI: Không rõ action
  async function get() {}
  async function set() {}
  async function load() {}
})
```

---

## 3. SSR Considerations

### 3.1 Pinia trong SSR

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],

  pinia: {
    storesDirs: ['./stores/**']
  }
})
```

### 3.2 Server-side State

```typescript
export const useUserStore = defineStore('user', () => {
  const user = useState('user', () => null)

  async function fetchUser() {
    // Fetch user from API
    user.value = await $fetch('/api/user/me')
  }

  return { user, fetchUser }
})
```

### 3.3 Hydration

```vue
<script setup lang="ts">
// Client-side: Fetch user khi hydrating
const userStore = useUserStore()

onMounted(async () => {
  if (!userStore.user) {
    await userStore.fetchUser()
  }
})
</script>
```

---

## 4. Performance Tips

### 4.1 storeToRefs

```vue
<script setup lang="ts">
import { storeToRefs } from 'pinia'

const userStore = useUserStore()

// ✅ ĐÚNG: Giữ reactivity khi destructuring
const { user, isAuthenticated } = storeToRefs(userStore)

// Actions không cần storeToRefs
const { fetchUser, logout } = userStore
</script>
```

### 4.2 Avoid Unnecessary Re-renders

```typescript
// ❌ SAI: Return raw ref
return {
  items: ref([])  // Mỗi thay đổi trigger re-render
}

// ✅ ĐÚNG: Return readonly hoặc computed
const items = ref<Item[]>([])

return {
  items,  // Vẫn reactive
  itemCount: computed(() => items.value.length)
}
```

### 4.3 Batch Updates

```typescript
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])

  // ❌ SAI: Nhiều mutations riêng lẻ
  function replaceCart(newItems: CartItem[]) {
    items.value = []
    newItems.forEach(item => items.value.push(item))
  }

  // ✅ ĐÚNG: Batch update
  function replaceCart(newItems: CartItem[]) {
    items.value = newItems
  }
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PINIA BEST PRACTICES                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Dùng SETUP STORE pattern (defineStore với () => {})         │
│  ✅ Đặt tên: useXxxStore                                          │
│  ✅ Dùng storeToRefs cho reactive destructuring                   │
│  ✅ Phân chia stores theo feature                                  │
│  ✅ SSR: Dùng useState cho shared state                           │
│  ✅ Computed cho derived state                                     │
│  ✅ Async actions với try/catch                                    │
│                                                                     │
│  ❌ KHÔNG: Destructure trực tiếp từ store                       │
│  ❌ KHÔNG: Mutation trong computed                                 │
│  ❌ KHÔNG: Side effects trong getters                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tham Khảo

→ [Pinia Docs](https://pinia.vuejs.org/)

# 4. Kỹ thuật Nâng cao & Data Fetching kết hợp Pinia

## 4.1. Data Fetching với useFetch / $fetch trong Actions

### Sự khác biệt giữa useFetch và $fetch

```
┌─────────────────────────────────────────────────────────────────────┐
│                     FETCHING COMPARISON                               │
│                                                                      │
│  $fetch                           useFetch                           │
│  ───────────                      ───────────                        │
│  • Raw HTTP client                • Nuxt wrapper                    │
│  • Chủ động gọi API              • Auto tracking, caching         │
│  • Cần tự quản lý loading/error  • Tự động loading/error state    │
│  • Tốt cho: actions, mutations  • Tốt cho: page-level fetching   │
│  • SSR-safe (relative URLs)      • SSR-safe (relative URLs)        │
│                                                                      │
│  ✅ Trong Pinia Actions → Dùng $fetch                               │
│  ✅ Trong Pages/Components → Dùng useFetch                         │
└─────────────────────────────────────────────────────────────────────┘
```

### Data Fetching trong Pinia Actions

```typescript
// stores/product.ts
import { defineStore } from 'pinia'
import type { Product, ProductFilter } from '~/types'

export const useProductStore = defineStore('products', () => {
  // ===== STATE =====
  const products = ref<Product[]>([])
  const currentProduct = ref<Product | null>(null)
  const isLoading = ref(false)
  const error = ref<string | null>(null)
  const filters = ref<ProductFilter>({
    category: null,
    minPrice: null,
    maxPrice: null,
    search: ''
  })

  // ===== GETTERS =====
  const filteredProducts = computed(() => {
    let result = products.value

    if (filters.value.category) {
      result = result.filter(p => p.category === filters.value.category)
    }

    if (filters.value.minPrice !== null) {
      result = result.filter(p => p.price >= filters.value.minPrice!)
    }

    if (filters.value.maxPrice !== null) {
      result = result.filter(p => p.price <= filters.value.maxPrice!)
    }

    if (filters.value.search) {
      const search = filters.value.search.toLowerCase()
      result = result.filter(p =>
        p.name.toLowerCase().includes(search) ||
        p.description.toLowerCase().includes(search)
      )
    }

    return result
  })

  const productCount = computed(() => filteredProducts.value.length)
  const averagePrice = computed(() => {
    if (filteredProducts.value.length === 0) return 0
    const sum = filteredProducts.value.reduce((acc, p) => acc + p.price, 0)
    return sum / filteredProducts.value.length
  })

  // ===== ACTIONS =====
  async function fetchProducts() {
    isLoading.value = true
    error.value = null

    try {
      // ✅ $fetch - SSR-safe, handle relative URLs
      const response = await $fetch<{ products: Product[] }>('/api/products', {
        params: filters.value
      })
      products.value = response.products
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to fetch products'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  async function fetchProductById(id: string) {
    isLoading.value = true
    error.value = null

    try {
      currentProduct.value = await $fetch<Product>(`/api/products/${id}`)
      return currentProduct.value
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Product not found'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  async function createProduct(data: Omit<Product, 'id'>) {
    isLoading.value = true
    error.value = null

    try {
      const newProduct = await $fetch<Product>('/api/products', {
        method: 'POST',
        body: data
      })
      products.value.push(newProduct)
      return newProduct
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to create product'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  async function updateProduct(id: string, data: Partial<Product>) {
    isLoading.value = true
    error.value = null

    try {
      const updated = await $fetch<Product>(`/api/products/${id}`, {
        method: 'PATCH',
        body: data
      })

      const index = products.value.findIndex(p => p.id === id)
      if (index > -1) {
        products.value[index] = updated
      }

      if (currentProduct.value?.id === id) {
        currentProduct.value = updated
      }

      return updated
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to update product'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  async function deleteProduct(id: string) {
    isLoading.value = true
    error.value = null

    try {
      await $fetch(`/api/products/${id}`, { method: 'DELETE' })
      products.value = products.value.filter(p => p.id !== id)

      if (currentProduct.value?.id === id) {
        currentProduct.value = null
      }
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to delete product'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  function setFilters(newFilters: Partial<ProductFilter>) {
    filters.value = { ...filters.value, ...newFilters }
  }

  function clearFilters() {
    filters.value = {
      category: null,
      minPrice: null,
      maxPrice: null,
      search: ''
    }
  }

  return {
    // State
    products,
    currentProduct,
    isLoading,
    error,
    filters,
    // Getters
    filteredProducts,
    productCount,
    averagePrice,
    // Actions
    fetchProducts,
    fetchProductById,
    createProduct,
    updateProduct,
    deleteProduct,
    setFilters,
    clearFilters
  }
})
```

### Kết hợp useFetch trong Pages với Pinia Store

```vue
<!-- pages/products/index.vue -->
<script setup lang="ts">
const productStore = useProductStore()

// ✅ useFetch cho page-level data (auto-caching, reactive params)
const { data: categories } = await useFetch('/api/categories')

// Fetch products khi filter thay đổi
watch(
  () => productStore.filters,
  async (newFilters) => {
    await productStore.fetchProducts()
  },
  { deep: true }
)

// Initial fetch
await productStore.fetchProducts()
</script>

<template>
  <div>
    <!-- Filters -->
    <select v-model="productStore.filters.category">
      <option :value="null">All Categories</option>
      <option v-for="cat in categories" :key="cat.id" :value="cat.id">
        {{ cat.name }}
      </option>
    </select>

    <!-- Products Grid -->
    <div v-if="productStore.isLoading">Loading...</div>
    <div v-else-if="productStore.error">{{ productStore.error }}</div>
    <div v-else>
      <p>{{ productStore.productCount }} products found</p>
      <ProductCard
        v-for="product in productStore.filteredProducts"
        :key="product.id"
        :product="product"
      />
    </div>
  </div>
</template>
```

---

## 4.2. Persist State với LocalStorage / Cookies

### Sơ đồ Persistence Strategy

```
┌─────────────────────────────────────────────────────────────────────┐
│                   PERSISTENCE STRATEGY                                │
│                                                                      │
│  Server-Side (SSR)              Client-Side                         │
│  ──────────────────             ───────────                          │
│  • useCookie() ←──────────────→ Same cookie                         │
│  • Safe to use                 • Safe to use                        │
│  • HttpOnly, secure            • Read/write data                    │
│                                                                      │
│  ❌ localStorage ←─────────────── ⚠️ localStorage                  │
│     NOT accessible on server    • Window-dependent                  │
│                                  • Need check import.meta.client     │
└─────────────────────────────────────────────────────────────────────┘
```

### Persistence với useCookie (SSR-Safe)

```typescript
// stores/preferences.ts
export const usePreferencesStore = defineStore('preferences', () => {
  // ✅ Dùng useCookie - SSR-safe, sync giữa server/client
  const theme = useCookie<'light' | 'dark'>('preferences_theme', {
    default: () => 'light',
    maxAge: 60 * 60 * 24 * 365, // 1 year
    watch: true // Auto-sync changes
  })

  const language = useCookie<string>('preferences_language', {
    default: () => 'en',
    maxAge: 60 * 60 * 24 * 365
  })

  const notifications = useCookie<boolean>('preferences_notifications', {
    default: () => true,
    maxAge: 60 * 60 * 24 * 365
  })

  // Computed từ cookie
  const isDarkMode = computed(() => theme.value === 'dark')

  // Actions
  function setTheme(newTheme: 'light' | 'dark') {
    theme.value = newTheme
  }

  function toggleTheme() {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
  }

  function setLanguage(lang: string) {
    language.value = lang
  }

  function setNotifications(enabled: boolean) {
    notifications.value = enabled
  }

  return {
    theme,
    language,
    notifications,
    isDarkMode,
    setTheme,
    toggleTheme,
    setLanguage,
    setNotifications
  }
})
```

### Persistence với LocalStorage (Client-only)

```typescript
// stores/cart.client.ts - Client-only store
// .client suffix = chỉ load trên client

export const useLocalCartStore = defineStore('localCart', () => {
  // ===== STATE =====
  // Khởi tạo rỗng - sẽ load từ localStorage trong onMounted
  const items = ref<CartItem[]>([])
  const lastSyncTime = ref<number | null>(null)

  // ===== HYDRATION FROM LOCALSTORAGE =====
  // Chỉ chạy trên client
  function loadFromStorage() {
    if (import.meta.server) return

    try {
      const stored = localStorage.getItem('cart_items')
      if (stored) {
        items.value = JSON.parse(stored)
        lastSyncTime.value = Date.now()
      }
    } catch (e) {
      console.error('Failed to load cart from localStorage:', e)
    }
  }

  // Save to localStorage
  function saveToStorage() {
    if (import.meta.server) return

    try {
      localStorage.setItem('cart_items', JSON.stringify(items.value))
      lastSyncTime.value = Date.now()
    } catch (e) {
      console.error('Failed to save cart to localStorage:', e)
    }
  }

  // ===== COMPUTED =====
  const itemCount = computed(() => items.value.reduce((sum, i) => sum + i.quantity, 0))
  const totalPrice = computed(() =>
    items.value.reduce((sum, i) => sum + i.price * i.quantity, 0)
  )
  const isEmpty = computed(() => items.value.length === 0)

  // ===== ACTIONS =====
  function addItem(item: CartItem) {
    const existing = items.value.find(i => i.id === item.id)
    if (existing) {
      existing.quantity += item.quantity
    } else {
      items.value.push({ ...item })
    }
    saveToStorage()
  }

  function removeItem(id: number) {
    items.value = items.value.filter(i => i.id !== id)
    saveToStorage()
  }

  function updateQuantity(id: number, quantity: number) {
    if (quantity <= 0) {
      removeItem(id)
      return
    }
    const item = items.value.find(i => i.id === id)
    if (item) {
      item.quantity = quantity
      saveToStorage()
    }
  }

  function clearCart() {
    items.value = []
    saveToStorage()
  }

  return {
    items,
    lastSyncTime,
    itemCount,
    totalPrice,
    isEmpty,
    loadFromStorage,
    addItem,
    removeItem,
    updateQuantity,
    clearCart
  }
})
```

```vue
<!-- components/CartSidebar.vue -->
<script setup lang="ts">
const localCartStore = useLocalCartStore()

// Load từ localStorage khi mount
onMounted(() => {
  localCartStore.loadFromStorage()
})
</script>

<template>
  <div>
    <p>Items: {{ localCartStore.itemCount }}</p>
    <p>Total: ${{ localCartStore.totalPrice }}</p>
  </div>
</template>
```

### Combined Approach: Pinia Plugin + LocalStorage

```typescript
// plugins/pinia-persist.client.ts
// Client-only plugin để persist tất cả stores

export default defineNuxtPlugin(() => {
  // Only run on client
  if (import.meta.server) return

  const pinia = usePinia()

  pinia.use(({ store }) => {
    // Chỉ persist stores có option persist: true
    if (!store.$options.persist) return

    const storageKey = `pinia_${store.$id}`

    // Load from localStorage
    const storedState = localStorage.getItem(storageKey)
    if (storedState) {
      try {
        store.$patch(JSON.parse(storedState))
      } catch (e) {
        console.error(`Failed to hydrate ${store.$id}:`, e)
      }
    }

    // Subscribe to changes
    store.$subscribe((mutation, state) => {
      localStorage.setItem(storageKey, JSON.stringify(state))
    }, { detached: true })
  })
})
```

```typescript
// stores/user.ts - Thêm persist option
export const useUserStore = defineStore('user', {
  // ✅ Option để plugin nhận biết
  persist: true,

  state: () => ({
    user: null as User | null,
    preferences: {} as Record<string, any>
  }),

  // ... rest of store
})
```

---

## 4.3. Pinia Plugins và Nuxt Plugins

### Sơ đồ Plugin Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                      PINIA PLUGIN SYSTEM                             │
│                                                                      │
│  Plugin                                                            │
│    ↓                                                               │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │  ({ store, ...options }) => {                               │  │
│  │    // Add properties to ALL stores                         │  │
│  │    store.$persist = (...)                                   │  │
│  │                                                              │  │
│  │    // Hook into store lifecycle                             │  │
│  │    store.$onAction(({ name, args, after, onError }) => {   │  │
│  │      console.log(`${name} called with ${args}`)             │  │
│  │    })                                                       │  │
│  │  }                                                          │  │
│  └─────────────────────────────────────────────────────────────┘  │
│    ↓                                                               │
│  Applied to ALL stores                                             │
└─────────────────────────────────────────────────────────────────────┘
```

### Tạo Pinia Plugin trong Nuxt

```typescript
// plugins/pinia.ts (hoặc plugins/pinia/index.ts)
export default defineNuxtPlugin((nuxtApp) => {
  const pinia = usePinia()

  // Install plugin
  pinia.use(({ store }) => {
    // 1. Add $reset method nếu thiếu (Setup store)
    if ('$reset' in store === false) {
      store.$reset = () => {
        const initialState = store.$options.state?.()
        if (initialState) {
          store.$patch(initialState)
        }
      }
    }

    // 2. Add $subscribe for logging (dev only)
    if (process.dev) {
      store.$subscribe((mutation, state) => {
        console.log(`[Pinia] ${store.$id}:`, {
          type: mutation.type,
          events: mutation.events
        })
      })
    }

    // 3. Add timestamp tracking
    if (!store.$options.noTimestamp) {
      store._createdAt = Date.now()
      store._updatedAt = Date.now()

      store.$onAction(({ name, after }) => {
        after(() => {
          store._updatedAt = Date.now()
        })
      })
    }
  })
})
```

### Plugin cho API Calls

```typescript
// plugins/pinia-api.ts
export default defineNuxtPlugin((nuxtApp) => {
  const pinia = usePinia()

  pinia.use(({ store }) => {
    // Wrapper for async actions với loading/error handling
    const originalActions = { ...store.$options.actions }

    // Add $action wrapper
    store.$executeAction = async function<T>(
      actionName: string,
      actionFn: () => Promise<T>
    ): Promise<T> {
      const store = this as any

      // Set loading state nếu có
      if ('isLoading' in store) {
        (store as any).isLoading = true
      }

      // Clear error
      if ('error' in store) {
        (store as any).error = null
      }

      try {
        const result = await actionFn()
        return result
      } catch (e) {
        if ('error' in store) {
          (store as any).error = e instanceof Error ? e.message : 'Unknown error'
        }
        throw e
      } finally {
        if ('isLoading' in store) {
          (store as any).isLoading = false
        }
      }
    }
  })
})
```

### Plugin cho Server-Side State

```typescript
// plugins/pinia-ssr.client.ts
export default defineNuxtPlugin(() => {
  const pinia = usePinia()
  const nuxtApp = useNuxtApp()

  // Đợi hydration hoàn tất
  nuxtApp.hook('app:mounted', () => {
    console.log('[Pinia] Hydration complete')

    // Trigger re-fetch cho data cần real-time
    const stores = pinia.state.value
    console.log('[Pinia] Active stores:', Object.keys(stores))
  })
})
```

### Store có thể tùy chỉnh Plugin Options

```typescript
// stores/user.ts - Store với custom options
export const useUserStore = defineStore('user', {
  // Custom options
  noTimestamp: true,
  persist: true,

  state: () => ({
    user: null as User | null,
    token: null as string | null
  }),

  getters: {
    isAuthenticated: (state) => !!state.token
  },

  actions: {
    async login(credentials: LoginCredentials) {
      const response = await $fetch<{ user: User; token: string }>('/api/auth/login', {
        method: 'POST',
        body: credentials
      })

      this.user = response.user
      this.token = response.token
    },

    logout() {
      this.user = null
      this.token = null
    }
  }
})
```

---

## 4.4. Error Handling và Retry Logic

```typescript
// composables/useRetry.ts
export function useRetry<T>(
  fn: () => Promise<T>,
  options: {
    maxRetries?: number
    delay?: number
    onRetry?: (attempt: number, error: Error) => void
  } = {}
) {
  const { maxRetries = 3, delay = 1000, onRetry } = options

  return async function retry(...args: any[]): Promise<T> {
    let lastError: Error

    for (let attempt = 0; attempt <= maxRetries; attempt++) {
      try {
        return await fn()
      } catch (e) {
        lastError = e instanceof Error ? e : new Error(String(e))

        if (attempt < maxRetries) {
          onRetry?.(attempt + 1, lastError)
          await new Promise(resolve => setTimeout(resolve, delay * (attempt + 1)))
        }
      }
    }

    throw lastError!
  }
}

// stores/product.ts - Enhanced với retry
export const useProductStore = defineStore('products', () => {
  const products = ref<Product[]>([])
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  const retryFetch = useRetry(
    () => $fetch<Product[]>('/api/products'),
    {
      maxRetries: 3,
      delay: 1000,
      onRetry: (attempt, error) => {
        console.warn(`Retry attempt ${attempt}:`, error.message)
      }
    }
  )

  async function fetchProducts() {
    isLoading.value = true
    error.value = null

    try {
      products.value = await retryFetch()
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to fetch products'
    } finally {
      isLoading.value = false
    }
  }

  return { products, isLoading, error, fetchProducts }
})
```

---

## 4.5. Tóm tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ADVANCED PATTERNS CHEAT SHEET                     │
│                                                                      │
│  ✅ Data Fetching                                                    │
│     ├── Actions: Dùng $fetch()                                       │
│     ├── Pages: Dùng useFetch() với await                           │
│     └── Xử lý error trong try/catch                                 │
│                                                                      │
│  ✅ Persistence                                                      │
│     ├── Simple data: useCookie() - SSR-safe                        │
│     ├── Complex data: Pinia plugin + localStorage                   │
│     └── Client-only code: import.meta.client check                  │
│                                                                      │
│  ✅ Pinia Plugins                                                    │
│     ├── plugins/pinia.ts - Shared logic cho all stores              │
│     ├── plugins/pinia.client.ts - Client-only plugins              │
│     └── store.$subscribe() - Watch state changes                   │
│                                                                      │
│  ✅ Error Handling                                                   │
│     ├── Try/catch trong actions                                      │
│     ├── Set error state + throw                                      │
│     └── useRetry composable cho retries                             │
└─────────────────────────────────────────────────────────────────────┘
```

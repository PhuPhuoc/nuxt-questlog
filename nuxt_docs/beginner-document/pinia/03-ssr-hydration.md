# 3. Pinia trong Môi trường SSR & Xử lý Hydration

## 3.1. Vấn đề Cross-Request State Pollution

### Bản chất của vấn đề

```
┌─────────────────────────────────────────────────────────────────────┐
│              SERVER-SIDE RENDERING (SSR) ARHITEKTUR                  │
│                                                                      │
│   User A Request                    User B Request                    │
│   ┌─────────────────┐              ┌─────────────────┐              │
│   │   Server Node   │              │   Server Node   │              │
│   │                 │              │                 │              │
│   │  ┌───────────┐ │              │  ┌───────────┐ │              │
│   │  │ Pinia     │ │              │  │ Pinia     │ │              │
│   │  │ Store     │ │              │  │ Store     │ │              │
│   │  │           │ │              │  │           │ │              │
│   │  │ user: "A" │ │              │  │ user: "B" │ │              │
│   │  └───────────┘ │              │  └───────────┘ │              │
│   │       ↓        │              │       ↓        │              │
│   │  HTML Render   │              │  HTML Render   │              │
│   │       ↓        │              │       ↓        │              │
│   │  Send to A     │              │  Send to B     │              │
│   └─────────────────┘              └─────────────────┘              │
│                                                                      │
│   ⚠️ NẾU dùng shared mutable state → User A có thể thấy data của   │
│      User B (State Leak / State Pollution)                          │
└─────────────────────────────────────────────────────────────────────┘
```

### Ví dụ về State Pollution

```typescript
// ❌ SAI: Shared mutable state gây pollution
const globalStore = {
  user: null  // ⚠️ SHARED giữa TẤT CẢ requests!
}

// Request A: Set user = "Alice"
globalStore.user = "Alice"

// Request B: user = "Bob" (ghi đè!)
globalStore.user = "Bob"

// Response A về browser → vẫn thấy "Bob" thay vì "Alice"!
```

### Giải pháp của Nuxt/Pinia

```typescript
// ✅ ĐÚNG: Mỗi request có state riêng biệt
export const useRequestUser = (event: H3Event) => {
  // Tạo state mới cho mỗi request
  return useState('user', () => null)
}

// Hoặc với Pinia trong Nuxt - tự động xử lý!
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)  // ✅ Mỗi request có instance riêng
  return { user }
})
```

---

## 3.2. Luồng State từ Server → Client (Hydration)

### Sơ đồ Hydration

```
┌─────────────────────────────────────────────────────────────────────┐
│                        SSR HYDRATION FLOW                            │
│                                                                      │
│  1. SERVER SIDE                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  a) Nuxt tạo fresh Pinia instance cho request này            │  │
│  │  b) Store được khởi tạo với initial state                    │  │
│  │  c) Components render, gọi actions để fetch data          │  │
│  │  d) State hoàn chỉnh                                          │  │
│  │  e) Nuxt serialize state vào HTML                            │  │
│  │                                                             │  │
│  │     <script>window.__NUXT__ = {                             │  │
│  │       pinia: { auth: { user: {...}, token: "abc123" } }     │  │
│  │     }</script>                                               │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                              ↓                                       │
│  2. CLIENT SIDE (Browser)                                           │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  a) Browser nhận HTML (đã có state serialized)               │  │
│  │  b) Vue/Nuxt khởi tộng, tạo Pinia instance mới              │  │
│  │  c) Pinia nhận state từ window.__NUXT__.pinia                │  │
│  │  d) Pinia hydrate: replace initial state = server state       │  │
│  │  e) Vue hydration: mount components lên HTML đã render       │  │
│  │  f) Vue hydrate: gắn event listeners vào existing DOM        │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  3. CLIENT NAVIGATION (SPA Mode)                                    │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  a) User navigate qua lại trang                               │  │
│  │  b) Client-side routing                                        │  │
│  │  c) Pinia instance giữ nguyên (shared state)                 │  │
│  │  d) KHÔNG cần hydrate lại                                    │  │
│  └───────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### Chi tiết từng bước

#### Bước 1-2: Server khởi tạo Store

```typescript
// server/api/auth/login.post.ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  const user = await authenticateUser(body)

  // Set cookie để duy trì session
  setCookie(event, 'auth_token', user.token, {
    httpOnly: true,
    secure: true,
    sameSite: 'lax',
    maxAge: 60 * 60 * 24 * 7 // 1 week
  })

  return { user, token: user.token }
})
```

```vue
<!-- pages/login.vue -->
<script setup lang="ts">
const authStore = useAuthStore()

// ✅ Chạy trên SERVER khi SSR
await authStore.login(credentials)

// ✅ State đã được set, sẵn sàng serialize
</script>

<template>
  <!-- Server render với user đã login -->
  <div>Welcome, {{ authStore.user?.name }}!</div>
</template>
```

#### Bước 3-4: Serialize State vào HTML

```html
<!-- Generated HTML trên Server -->
<!DOCTYPE html>
<html>
<head>
  <!-- ... -->
</head>
<body>
  <div id="__nuxt">Welcome, Alice!</div>

  <!-- ⬇️ STATE ĐƯỢC SERIALIZE Ở ĐÂY ⬇️ -->
  <script>
    window.__NUXT__ = {
      serverRendered: true,
      state: {
        pinia: {
          auth: {
            user: { id: 1, name: "Alice", email: "alice@example.com" },
            token: "eyJhbGciOiJIUzI1NiIs..."
          }
        }
      }
    }
  </script>
</body>
</html>
```

#### Bước 5-6: Client Hydrate

```typescript
// Client-side initialization (tự động bởi @pinia/nuxt)
const pinia = createPinia()

// Lấy state từ server
const serverState = window.__NUXT__?.state?.pinia

if (serverState) {
  // Replace initial state với server state
  pinia.state.value = serverState
}

const app = createApp(App)
app.use(pinia)
app.mount('#app')

// Bây giờ client có đầy đủ state từ server!
console.log(authStore.user) // { id: 1, name: "Alice", ... }
```

### Quá trình Hydration hoàn chỉnh

```
┌─────────────────────────────────────────────────────────────────────┐
│                    HYDRATION TIMELINE                               │
│                                                                      │
│  Server                                                            │
│  ────────                                                          │
│  0ms  │ useAuthStore() → user: null                                │
│  50ms │ await $fetch('/api/me') → user: { name: "Alice" }        │
│  100ms│ HTML rendered with "Welcome, Alice!"                       │
│  150ms│ State serialized to window.__NUXT__                        │
│                                                                      │
│                        ↓ (Network Transfer)                          │
│                                                                      │
│  Client                                                            │
│  ────────                                                          │
│  0ms   │ HTML received (pre-rendered)                              │
│  10ms  │ JS bundle loading...                                       │
│  100ms │ Pinia initialized                                         │
│  110ms │ ⚡ Hydration: user: { name: "Alice" }                     │
│  120ms │ Vue hydrates existing DOM                                 │
│  130ms │ Page interactive (hydration complete)                    │
│  150ms │ User clicks logout → client-side navigation               │
│                                                                      │
│  Result: Không có flash, không mất dữ liệu!                         │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3.3. storeToRefs() - Destructuring mà không mất Reactive

### Vấn đề với direct destructuring

```typescript
const authStore = useAuthStore()

// ❌ SAI: Mất reactivity!
const { user, isLoggedIn } = authStore

// Vì sao?
// - authStore.user là một ref
// - Destructuring lấy .value (unwrap)
// - Biến local user không còn là ref
// - Vue không track nó nữa!

console.log(typeof user)        // "object" hoặc "User" - KHÔNG reactive
console.log(typeof isLoggedIn)  // "boolean" - đã unwrap!
```

### Giải pháp: storeToRefs()

```typescript
import { storeToRefs } from 'pinia'

const authStore = useAuthStore()

// ✅ ĐÚNG: Dùng storeToRefs
const { user, isLoggedIn } = storeToRefs(authStore)

// - user vẫn là ComputedRef
// - isLoggedIn vẫn là ComputedRef
// - Reactive!

// Dùng trong template
console.log(user.value)          // { id: 1, name: "Alice" }
console.log(isLoggedIn.value)    // true

// HOẶC trong script, không cần .value (tự unwrap)
if (isLoggedIn) {  // tự unwrap!
  console.log(user.name)  // tự unwrap!
}
```

### Phân biệt storeToRefs vs toRefs

```typescript
import { toRefs } from 'vue'
import { storeToRefs } from 'pinia'

const authStore = useAuthStore()

// toRefs (Vue) - Chỉ unwrap ref
// - Biến thành ref nhưng KHÔNG reactive với store changes
const refs1 = toRefs(authStore)
// refs1.user.value sẽ KHÔNG update khi store thay đổi!

// storeToRefs (Pinia) - Unwrap + Reactive
// - Biến vẫn reactive với store changes
const refs2 = storeToRefs(authStore)
// refs2.user.value sẽ UPDATE khi store thay đổi!

// Ví dụ minh họa:
watchEffect(() => {
  console.log(authStore.user)     // ✅ Reactive - trigger khi store.user thay đổi
  console.log(user.value)         // ✅ Reactive - trigger khi store.user thay đổi
})
```

### Chi tiết implementation của storeToRefs

```typescript
// Cách storeToRefs hoạt động (simplified)
function storeToRefs(store) {
  const refs = {}

  for (const key in store) {
    const value = store[key]

    if (isRef(value) || isReactive(value)) {
      // Chỉ unwrap nhưng GIỮ refs
      refs[key] = toRef(store, key)
    }
  }

  return refs
}

// Result:
// - State: computed ref → giữ reactive
// - Getters: computed ref → giữ reactive
// - Actions: function → giữ nguyên (vì function không phải ref)
```

### Best Practice sử dụng storeToRefs

```vue
<script setup lang="ts">
const cartStore = useCartStore()

// ==== STATE & GETTERS ====
// ✅ Dùng storeToRefs cho state và getters
const { items, itemCount, totalPrice, isEmpty } = storeToRefs(cartStore)

// ==== ACTIONS ====
// ✅ Actions không cần storeToRefs
const { addItem, removeItem, clearCart } = cartStore

// ==== HOẶC: Không destructuring ====
// Cũng OK, nhưng dài hơn
const count = cartStore.itemCount
const total = cartStore.totalPrice
</script>

<template>
  <!-- Dùng trong template - tự unwrap -->
  <div v-if="!isEmpty">
    <p>Items: {{ itemCount }}</p>
    <p>Total: ${{ totalPrice }}</p>
  </div>

  <button @click="addItem({ id: 1, name: 'Product', price: 100 })">
    Add to Cart
  </button>
</template>
```

### Reactive Store (không cần storeToRefs)

```typescript
// Setup Store - Có thể trả về reactive object
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])

  // return với reactive()
  return reactive({
    items,
    get itemCount() { return items.value.length },
    get total() { return items.value.reduce((sum, i) => sum + i.price, 0) },
    addItem(item: CartItem) { items.value.push(item) },
    removeItem(id: number) {
      const index = items.value.findIndex(i => i.id === id)
      if (index > -1) items.value.splice(index, 1)
    }
  })
})
```

```vue
<script setup lang="ts">
// ✅ Khi dùng reactive(), không cần storeToRefs
const cart = useCartStore()

// cart.items, cart.itemCount, cart.total vẫn reactive
// cart.addItem, cart.removeItem vẫn là methods
</script>

<template>
  <p>{{ cart.itemCount }}</p>
  <button @click="cart.addItem({ id: 1 })">Add</button>
</template>
```

---

## 3.4. SSR-Safe Data Fetching

### Vấn đề với Side Effects trên Server

```typescript
// ❌ SAI: Gây memory leak hoặc crash trên server
export const useAuthStore = defineStore('auth', () => {
  // ⚠️ This chạy khi store được import
  // Trên server, mỗi request import module một lần
  // → Gây race condition / memory leak!

  // Ví dụ: localStorage access
  const savedToken = localStorage.getItem('token')  // ❌ window is not defined!
  const token = ref(savedToken)

  return { token }
})
```

### Giải pháp SSR-Safe

```typescript
// ✅ ĐÚNG: SSR-Safe
export const useAuthStore = defineStore('auth', () => {
  const token = useCookie('auth_token', {
    default: () => null,
    maxAge: 60 * 60 * 24 * 7,
    watch: true  // Sync với client
  })

  // HOẶC dùng onMounted cho client-only code
  const userPrefs = ref<UserPrefs | null>(null)

  const loadUserPrefs = () => {
    // Chỉ chạy trên client
    if (import.meta.client) {
      userPrefs.value = JSON.parse(localStorage.getItem('prefs') || 'null')
    }
  }

  return { token, userPrefs, loadUserPrefs }
})
```

### Data Fetching trong Actions (SSR-Safe)

```typescript
export const useProductStore = defineStore('products', () => {
  const products = ref<Product[]>([])
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  async function fetchProducts() {
    isLoading.value = true
    error.value = null

    try {
      // $fetch là SSR-safe trong Nuxt
      // Tự động xử lý relative URLs
      // Server: gọi internal API
      // Client: gọi qua HTTP
      products.value = await $fetch<Product[]>('/api/products')
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'Failed to fetch'
      throw e
    } finally {
      isLoading.value = false
    }
  }

  return { products, isLoading, error, fetchProducts }
})
```

---

## 3.5. Tóm tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                        SSR HYDRATION CHEAT SHEET                    │
│                                                                      │
│  ✅ State Pollution Prevention                                       │
│     ├── Mỗi request có Pinia instance riêng                        │
│     └── @pinia/nuxt module tự động xử lý                           │
│                                                                      │
│  ✅ Hydration Flow                                                   │
│     ├── Server: Render + Serialize state to window.__NUXT__        │
│     ├── Client: Read state + Hydrate Pinia                         │
│     └── Client: Mount Vue + Hydrate DOM                             │
│                                                                      │
│  ✅ storeToRefs()                                                  │
│     ├── Dùng cho state & getters                                    │
│     ├── KHÔNG dùng cho actions                                      │
│     └── Khác với toRefs() - giữ reactive                            │
│                                                                      │
│  ✅ SSR-Safe                                                        │
│     ├── KHÔNG dùng window, localStorage trực tiếp                 │
│     ├── Dùng useCookie() thay vì localStorage                      │
│     └── Dùng $fetch() thay vì fetch() trực tiếp                    │
│                                                                      │
│  ✅ Data Fetching trong Actions                                      │
│     ├── Dùng $fetch() cho API calls                                 │
│     ├── Wrap trong try/catch                                       │
│     └── Set loading/error states                                    │
└─────────────────────────────────────────────────────────────────────┘
```

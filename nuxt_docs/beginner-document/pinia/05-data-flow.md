# 5. Sơ đồ Luồng Dữ liệu (State & Data Flow Visual Map)

## 5.1. Luồng hoàn chỉnh: User Action → UI Update

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPLETE DATA FLOW DIAGRAM                                │
│                                                                             │
│  USER TRIGGERS ACTION                                                        │
│           │                                                                  │
│           ▼                                                                  │
│  ┌─────────────────────┐                                                    │
│  │   Vue Component      │                                                    │
│  │   <script setup>    │                                                    │
│  │                     │                                                    │
│  │  const cart =       │  1. Lấy store instance                            │
│  │    useCartStore()   │                                                    │
│  │                     │                                                    │
│  │  cart.addItem(...)  │  2. Gọi action                                   │
│  └──────────┬──────────┘                                                    │
│             │                                                                │
│             ▼                                                                │
│  ┌─────────────────────┐                                                    │
│  │   Pinia Store       │                                                    │
│  │   useCartStore      │                                                    │
│  │                     │                                                    │
│  │  function addItem() │  3. Action logic                                  │
│  │    → Validate       │                                                    │
│  │    → Update state   │                                                    │
│  │    → Persist        │                                                    │
│  └──────────┬──────────┘                                                    │
│             │                                                                │
│             ▼                                                                │
│  ┌─────────────────────┐                                                    │
│  │   State Updated     │                                                    │
│  │   items: [...]     │  4. Reactive state thay đổi                        │
│  └──────────┬──────────┘                                                    │
│             │                                                                │
└─────────────┼─────────────────────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                      CLIENT-SIDE REACTIVITY                                  │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                     Vue Component Re-render                            │   │
│  │                                                                        │   │
│  │  <template>                                                          │   │
│  │    <div>{{ cart.itemCount }}</div>  ← Tự động update!               │   │
│  │    <div>{{ cart.total }}</div>      ← Tự động update!               │   │
│  │  </template>                                                         │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 5.2. SSR Hydration Flow (Chi tiết)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    SSR HYDRATION FLOW - STEP BY STEP                         │
│                                                                             │
│  ════════════════════════════════════════════════════════════════════════════  │
│  STEP 1: SERVER INITIALIZATION                                               │
│  ════════════════════════════════════════════════════════════════════════════  │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  Nuxt Server (Per Request)                                            │   │
│  │                                                                        │   │
│  │  1. Create Pinia instance ──→ New, clean state                        │   │
│  │  2. Render page ──→ useCartStore() called                           │   │
│  │  3. $fetch('/api/cart') ──→ Get user's cart from DB                 │   │
│  │  4. store.items = response ──→ State populated                       │   │
│  │  5. Render HTML with cart data                                       │   │
│  │                                                                        │   │
│  │  Final State: { items: [Product1, Product2], total: 150 }              │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  HTML Response to Browser                                             │   │
│  │                                                                        │   │
│  │  <div id="app">                                                       │   │
│  │    <div class="cart">                                                 │   │
│  │      <div>2 items - $150</div>  ← Pre-rendered content              │   │
│  │    </div>                                                             │   │
│  │  </div>                                                               │   │
│  │                                                                        │   │
│  │  <script>window.__NUXT__ = { pinia: { cart: { items: [...], ... } } }   │
│  │  </script>    ← Serialized state                                      │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
║═══════════════════════════════════════════════════════════════════════════║
║                                    ▼                                        ║
║  ════════════════════════════════════════════════════════════════════════════  ║
║  STEP 2: CLIENT HYDRATION                                                   ║
║  ════════════════════════════════════════════════════════════════════════════  ║
║                                                                             ║
║  ┌───────────────────────────────────────────────────────────────────────┐ ║
║  │  Browser                                                             │ ║
║  │                                                                      │ ║
║  │  1. Receive HTML (pre-rendered)                                      │ ║
║  │  2. Download JS bundles                                             │ ║
║  │  3. Create Pinia instance                                           │ ║
║  │  4. Hydrate Pinia ──→ Restore state from window.__NUXT__           │ ║
║  │     │                                                                │ ║
║  │     ▼                                                                │ ║
║  │     pinia.state.value = {                                           │ ║
║  │       cart: { items: [Product1, Product2], total: 150 }             │ ║
║  │     }                                                               │ ║
║  │  5. Mount Vue ──→ Vue hydrates DOM (no re-render!)                  │ ║
║  │  6. Page is interactive! ✓                                          │ ║
║  │                                                                      │ ║
║  │  Result: User thấy content NGAY, không có flash                      │ ║
║  └───────────────────────────────────────────────────────────────────────┘ ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

## 5.3. API Call Flow trong Action

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ACTION → API → STATE → UI FLOW                            │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  Vue Component                                                        │   │
│  │                                                                        │   │
│  │  await productStore.fetchProducts()  ← Trigger action                 │   │
│  │  ───────────────────────────────────────                              │   │
│  │  productStore.products  ← Reactive, UI tự update                      │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  Pinia Action (useProductStore)                                        │   │
│  │                                                                        │   │
│  │  async fetchProducts() {                                               │   │
│  │    1. this.isLoading = true                                           │   │
│  │    2. try {                                                          │   │
│  │    3.   const data = await $fetch('/api/products')  ← API Call       │   │
│  │    4.   this.products = data.products     ← Update State            │   │
│  │    5. } catch (e) {                                                   │   │
│  │    6.   this.error = e.message                                       │   │
│  │    7. } finally {                                                     │   │
│  │    8.   this.isLoading = false                                       │   │
│  │    9. }                                                               │   │
│  │  }                                                                    │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  State Change (Reactive)                                               │   │
│  │                                                                        │   │
│  │  products: Product[]  ← Reactive ref thay đổi                        │   │
│  │                                                                        │   │
│  │  Vue DevTools: "products" updated                                     │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  UI Updates (Automatic)                                                │   │
│  │                                                                        │   │
│  │  <template>                                                           │   │
│  │    <div v-if="productStore.isLoading">Loading...</div>                 │   │
│  │    <div v-else>                                                       │   │
│  │      <ProductCard                                                     │   │
│  │        v-for="p in productStore.products"                             │   │
│  │        :key="p.id"                                                   │   │
│  │        :product="p"                                                  │   │
│  │      />                                                               │   │
│  │    </div>                                                             │   │
│  │  </template>                                                         │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 5.4. Multi-Store Communication Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    MULTI-STORE COMMUNICATION                                 │
│                                                                             │
│  ┌─────────────────────────────────────┐                                   │
│  │  useAuthStore                        │                                   │
│  │  ├── user: User                     │                                   │
│  │  ├── token: string                  │                                   │
│  │  └── isAuthenticated: computed      │                                   │
│  └───────────────┬─────────────────────┘                                   │
│                  │ authStore.isAuthenticated                                 │
│                  │ (computed property)                                       │
│                  ▼                                                         │
│  ┌─────────────────────────────────────┐                                   │
│  │  useCartStore                        │                                   │
│  │                                       │                                   │
│  │  const authStore = useAuthStore()   │ ← Import auth store               │
│  │                                       │                                   │
│  │  watch(                              │ ← Watch auth changes              │
│  │    () => authStore.isAuthenticated, │                                   │
│  │    (isAuth) => {                     │                                   │
│  │      if (!isAuth) {                  │                                   │
│  │        clearCart()                   │ ← Auto-clear cart on logout       │
│  │      }                               │                                   │
│  │    }                                 │                                   │
│  │  )                                   │                                   │
│  │                                       │                                   │
│  │  actions:                            │                                   │
│  │  ├── checkout()                      │ ← Use token for API calls         │
│  │  │   const token = authStore.token  │                                   │
│  │  │   await $fetch('/api/checkout', {│                                   │
│  │  │     headers: { Authorization: ... }                                 │
│  │  │   })                              │                                   │
│  │  └── addItem()                       │ ← Only works if authenticated     │
│  └─────────────────────────────────────┘                                   │
│                                                                             │
│  Flow:                                                                     │
│  1. User login ──→ authStore.isAuthenticated = true                       │
│  2. cartStore.watch thấy thay đổi                                         │
│  3. cartStore vẫn giữ cart data                                           │
│  4. User logout ──→ authStore.isAuthenticated = false                      │
│  5. cartStore.watch thấy thay đổi ──→ clearCart()                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 5.5. Error Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ERROR HANDLING FLOW                                       │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  Action với Error Handling                                            │   │
│  │                                                                        │   │
│  │  async fetchProducts() {                                               │   │
│  │    isLoading.value = true                                              │   │
│  │    error.value = null                                                  │   │
│  │                                                                        │   │
│  │    try {                                                              │   │
│  │      products.value = await $fetch('/api/products')                  │   │
│  │    } catch (e) {  ◄──────────┐                                        │   │
│  │      error.value = e.message │                                        │   │
│  │      throw e                 │                                        │   │
│  │    } finally {               │                                        │   │
│  │      isLoading.value = false │                                        │   │
│  │    }                        │                                        │   │
│  └─────────────────────────────│─────────────────────────────────────────┘   │
│                                │                                              │
│                                ▼                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐   │
│  │  Component Error Display                                              │   │
│  │                                                                        │   │
│  │  <template>                                                           │   │
│  │    <div v-if="productStore.error" class="error">                     │   │
│  │      ⚠️ {{ productStore.error }}                                     │   │
│  │      <button @click="productStore.fetchProducts()">Retry</button>    │   │
│  │    </div>                                                             │   │
│  │  </template>                                                          │   │
│  └───────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 5.6. Complete Flow Summary

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPLETE FLOW - USER TO UI                                │
│                                                                             │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐             │
│  │   User   │───▶│ Component │───▶│  Store   │───▶│  API     │             │
│  │  Action  │    │ (Action) │    │ (State)  │    │ ($fetch) │             │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘             │
│                                           │                │                │
│                                           │                ▼                │
│                                           │          ┌──────────┐           │
│                                           │          │ Server   │           │
│                                           │          │ Database │           │
│                                           │          └──────────┘           │
│                                           │                │                │
│                                           ▼                ▼                │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐             │
│  │    UI    │◀───│ Template  │◀───│ Reactive │◀───│ Response │             │
│  │ Updated  │    │  Re-render│    │   State  │    │   Data   │             │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘             │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────   │
│                                                                             │
│  SSR Extension:                                                            │
│                                                                             │
│  Server ──▶ HTML (pre-rendered) ──▶ window.__NUXT__ (state)              │
│     │                                           │                          │
│     │                                           ▼                          │
│     │                                    Client Hydrate                     │
│     │                                           │                          │
│     │                                           ▼                          │
│     └────────────────────────────────────▶ UI Interactive                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

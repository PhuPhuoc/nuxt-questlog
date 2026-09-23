# Pinia Overview - State Management cho Nuxt

> **Mục tiêu:** Hiểu Pinia là gì và tại sao nên dùng trong Nuxt.

## Mục lục

1. [Pinia là gì?](#1-pinia-là-gì)
2. [So sánh Vuex vs Pinia](#2-so-sánh-vuex-vs-pinia)
3. [Tại sao dùng Pinia trong Nuxt?](#3-tại-sao-dùng-pinia-trong-nuxt)
4. [Cài đặt](#4-cài-đặt)

---

## 1. Pinia là gì?

### 1.1 Định nghĩa

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PINIA                                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Pinia = State Management Library cho Vue/Nuxt                     │
│                                                                     │
│  Thay thế Vuex (đã deprecated)                                     │
│  Official recommendation từ Vue core team                          │
│                                                                     │
│  Tính năng:                                                        │
│  ├── ✅ Simple API                                                 │
│  ├── ✅ TypeScript native                                           │
│  ├── ✅ DevTools integration                                       │
│  ├── ✅ SSR support                                                 │
│  └── ✅ Module-less design                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Store là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                         STORE                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Store = Nơi lưu trữ STATE + LOGIC                              │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── Auth Store: User, token, login/logout                        │
│  ├── Cart Store: Items, add/remove/checkout                       │
│  └── Product Store: Products, filters, CRUD                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. So Sánh Vuex vs Pinia

### 2.1 So sánh nhanh

| Tính năng | Vuex | Pinia |
|-----------|------|-------|
| API | Phức tạp | Đơn giản |
| TypeScript | Cần wrapper | Native support |
| Mutations | Có | Không có |
| Modules | Bắt buộc | Optional |
| DevTools | Hạn chế | Tốt |
| Vue 3 Support | Vue 2 only | Vue 3 native |

### 2.2 Vuex (cũ)

```typescript
// Vuex - Phức tạp
const store = createStore({
  state: () => ({ count: 0 }),
  mutations: {
    increment(state) { state.count++ }
  },
  actions: {
    async fetchCount({ commit }) {
      const count = await api.getCount()
      commit('increment', count)
    }
  },
  getters: {
    doubled: state => state.count * 2
  }
})

// Mutations bắt buộc, mutations phải synchronous
store.commit('increment')
store.dispatch('fetchCount')
```

### 2.3 Pinia (mới)

```typescript
// Pinia - Đơn giản
export const useCounterStore = defineStore('counter', () => {
  // State - như refs
  const count = ref(0)
  
  // Getters - như computed
  const doubled = computed(() => count.value * 2)
  
  // Actions - async functions
  async function fetchCount() {
    count.value = await api.getCount()
  }
  
  function increment() {
    count.value++
  }
  
  return { count, doubled, fetchCount, increment }
})

// Không cần mutations, gọi trực tiếp
store.increment()
await store.fetchCount()
```

---

## 3. Tại Sao Dùng Pinia Trong Nuxt?

### 3.1 SSR-Safe

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PINIA + NUXT SSR                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Pinia được @pinia/nuxt tích hợp sẵn                             │
│                                                                     │
│  ├── Mỗi request có Pinia instance riêng                         │
│  ├── State tự động serialize/deserialize                        │
│  └── Không có state pollution giữa users                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Auto-imports

```typescript
// Nuxt tự động import stores từ app/stores/
// stores/auth.ts → useAuthStore()

// Sử dụng không cần import!
const authStore = useAuthStore()
```

### 3.3 DevTools Integration

- Xem state trong Nuxt DevTools
- Time-travel debugging
- Track actions

---

## 4. Cài Đặt

### 4.1 Cài module

```bash
# Dùng nuxi (recommended)
npx nuxi@latest module add pinia

# Hoặc cài thủ công
npm install @pinia/nuxt pinia
```

### 4.2 Cấu hình nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
  
  pinia: {
    storesDirs: ['./stores/**']
  }
})
```

### 4.3 Tạo Store đầu tiên

```typescript
// app/stores/counter.ts
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  
  function increment() {
    count.value++
  }
  
  return { count, increment }
})
```

---

## ▶️ Tiếp Theo

→ [02-store-structure.md](02-store-structure.md) - Cấu trúc Store chi tiết

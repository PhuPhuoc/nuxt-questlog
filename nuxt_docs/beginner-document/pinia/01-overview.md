# 1. Tổng quan về State Management trong Nuxt

## 1.1. Tại sao Vuex bị thay thế bởi Pinia?

### Vuex - Đã lỗi thời trong Vue 3 / Nuxt 3

```
Vuex 3.x (Vue 2)          →          Vuex 4.x (Vue 3)
├── Mutex bắt buộc                  ├── Hỗ trợ Composition API
├── Boilerplate nhiều                ├── TypeScript support yếu
├── Không có auto-imports            ├── Vẫn cần mutation + action
└── Performance không tối ưu          └── Devtools vẫn phức tạp
```

**Vấn đề cốt lõi của Vuex:**
```typescript
// Vuex - Bắt buộc phải commit mutation
const store = createStore({
  state: { count: 0 },
  mutations: {
    increment(state) { state.count++ }  // ❌ Bước trung gian
  },
  actions: {
    async fetchUser({ commit }) {
      const user = await api.getUser()
      commit('SET_USER', user)  // ❌ Phải qua mutation
    }
  }
})
store.dispatch('fetchUser')  // ❌ Phải dispatch
```

### Pinia - Giải pháp hiện đại

```typescript
// Pinia - Thẳng tiến, TypeScript-first
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),
  actions: {
    async fetchUser() {
      this.user = await api.getUser()  // ✅ Gọi thẳng, không cần commit
    }
  }
})

const counter = useCounterStore()
counter.fetchUser()  // ✅ Gọi trực tiếp
counter.count++     // ✅ State có thể mutate trực tiếp
```

### So sánh chi tiết: Vuex vs Pinia

| Tiêu chí | Vuex 4 | Pinia |
|----------|--------|-------|
| Kiến trúc | Mutation → Action → State | Action → State (trực tiếp) |
| TypeScript | Phải khai báo kiểu rườm rà | Inference tự động |
| Boilerplate | Store, Mutation, Action, Getter | Chỉ State + Getter + Action |
| DevTools | Có nhưng phức tạp | Có, đơn giản hơn |
| API Surface | ~20+ methods | ~5 methods chính |
| Bundle Size | ~14kb | ~1kb |
| Module system | Phức tạp (namespaced) | Đơn giản (store là module) |

---

## 1.2. Phân biệt State nội bộ, Nuxt State, và Pinia Store

### Sơ đồ phân cấp sử dụng

```
┌─────────────────────────────────────────────────────────────────┐
│                        GLOBAL STATE                              │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    Pinia Store                             │  │
│  │  • Dùng khi: State cần chia sẻ GIỮA CÁC COMPONENTS        │  │
│  │  • Dùng khi: Cần persist qua SSR hydration                 │  │
│  │  • Dùng khi: Logic nghiệp vụ phức tạp (actions)            │  │
│  │  • Ví dụ: Auth, Cart, User Preferences, Products          │  │
│  └───────────────────────────────────────────────────────────┘  │
│                              ↕                                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                     useState()                            │  │
│  │  • Dùng khi: State cần SHARED trong 1 request (SSR)       │  │
│  │  • Dùng khi: State nhỏ, đơn giản, không cần actions      │  │
│  │  • Ví dụ: Toggle sidebar, notification queue              │  │
│  └───────────────────────────────────────────────────────────┘  │
│                              ↕                                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    ref() / reactive()                       │  │
│  │  • Dùng khi: State CHỈ TRONG 1 component                  │  │
│  │  • Dùng khi: State tạm thời, không cần chia sẻ            │  │
│  │  • Ví dụ: Local form state, temporary UI state            │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### Ví dụ cụ thể từng loại

#### 1️⃣ Local State (ref/reactive) - Component-level

```vue
<!-- components/Form.vue -->
<script setup lang="ts">
// ❌ SAI: State chỉ dùng trong component này - không cần Pinia
const formData = ref({ name: '', email: '' })
const isSubmitting = ref(false)

const handleSubmit = () => {
  isSubmitting.value = true
  // ... submit logic
}
</script>
```

#### 2️⃣ Shared State (useState) - Request-level (SSR)

```typescript
// composables/useNotifications.ts
export const useNotifications = () => {
  // ✅ Dùng useState - chia sẻ trong 1 request, SSR-safe
  const notifications = useState<Notification[]>('notifications', () => [])
  const unreadCount = useState<number>('unread-count', () => 0)

  const addNotification = (notification: Notification) => {
    notifications.value.push(notification)
    unreadCount.value++
  }

  return { notifications, unreadCount, addNotification }
}
```

#### 3️⃣ Global Store (Pinia) - Application-level

```typescript
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)
  const token = ref<string | null>(null)

  // ✅ Dùng Pinia - state phức tạp, cần actions, persist
  const login = async (credentials: LoginCredentials) => {
    const { user, token } = await $fetch('/api/auth/login', {
      method: 'POST',
      body: credentials
    })
    user.value = user
    token.value = token
  }

  const isAuthenticated = computed(() => !!token.value)

  return { user, token, login, isAuthenticated }
})
```

### Quy tắc quyết định

```
┌────────────────────────────────────────────────────────────────┐
│                    CÂY QUYẾT ĐỊNH                               │
│                                                                  │
│  State có cần persist / hydrate qua SSR không?                  │
│         ↓                                                         │
│    CÓ ──────→ Dùng Pinia (store + plugin) hoặc useState        │
│         │                                                         │
│    KHÔNG                                                         │
│         ↓                                                         │
│  State có cần chia sẻ giữa nhiều components không?               │
│         ↓                                                         │
│    CÓ ──────→ Dùng useState (đơn giản) hoặc Pinia (phức tạp) │
│         │                                                         │
│    KHÔNG                                                         │
│         ↓                                                         │
│  Dùng ref() / reactive() bình thường                           │
└────────────────────────────────────────────────────────────────┘
```

---

## 1.3. Bảng so sánh: Vue thuần vs Nuxt 3 với Pinia

### Vue 3 thuần (Vite)

```bash
npm install pinia
```

```typescript
// main.ts
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const app = createApp(App)
const pinia = createPinia()

app.use(pinia)
app.mount('#app')

// stores/user.ts
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    name: '',
    email: ''
  })
})
```

```vue
<!-- Component -->
<script setup lang="ts">
import { useUserStore } from '@/stores/user'  // ⚠️ Phải import thủ công

const userStore = useUserStore()
</script>
```

### Nuxt 3 với @pinia/nuxt

```bash
# Không cần install thủ công - Nuxt auto-setup
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
  pinia: {
    storesDirs: ['./stores/**']  // Thư mục stores auto-import
  }
})
```

```typescript
// stores/user.ts  - KHÔNG CẦN import defineStore!
export const useUserStore = defineStore('user', () => {
  // Setup Store style - TypeScript inference tự động
  const name = ref('')
  const email = ref('')

  const fullName = computed(() => `Mr. ${name.value}`)

  const fetchUser = async () => {
    const data = await $fetch('/api/user')
    name.value = data.name
    email.value = data.email
  }

  return { name, email, fullName, fetchUser }
})
```

```vue
<!-- Component - Không cần import! -->
<script setup lang="ts">
const userStore = useUserStore()  // ✅ Auto-imported!
</script>
```

### Bảng so sánh chi tiết

| Khía cạnh | Vue 3 (Vite) | Nuxt 3 (@pinia/nuxt) |
|-----------|--------------|---------------------|
| Cài đặt | `npm install pinia` | Thêm module vào config |
| Store discovery | Phải import thủ công | Auto-import từ `stores/` |
| SSR handling | Tự xử lý | Module tự động xử lý |
| Hydration | Phải config thủ công | Tự động hydrate |
| DevTools | Pinia DevTools riêng | Tích hợp Nuxt DevTools |
| TypeScript | Cần khai báo kiểu | Inference tự động |
| Hot Module Replacement | Cần config | Tự động |

### Hydration trong Vue thuần vs Nuxt

**Vue 3 thuần - Phải tự xử lý:**

```typescript
// main.ts - Xử lý SSR hydration thủ công
import { createSSRApp } from 'vue'
import { createPinia } from 'pinia'

export async function createApp() {
  const pinia = createPinia()
  const app = createSSRApp(App)

  // ⚠️ Phải restore state từ server
  if (window.__PINIA_STATE__) {
    pinia.state.value = window.__PINIA_STATE__
  }

  app.use(pinia)
  return { app, pinia }
}
```

**Nuxt 3 - Tự động xử lý:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
  // ✅ Module tự động:
  // 1. Serialize state vào window.__NUXT__
  // 2. Restore state trên client
  // 3. Xử lý cross-request pollution
})
```

---

## 1.4. Tóm tắt

```
┌──────────────────────────────────────────────────────────────────┐
│                      RECOMMENDATION SUMMARY                        │
│                                                                   │
│  ref() / reactive()          →  State cục bộ, đơn giản          │
│  useState()                  →  Shared state nhỏ, SSR request   │
│  Pinia Store                 →  Complex state, business logic,   │
│                                  persistence, TypeScript-heavy    │
│                                                                   │
│  Trong Nuxt 3: Pinia Store là lựa chọn MẶC ĐỊNH cho global state│
│  Trừ khi state quá đơn giản → dùng useState()                   │
└──────────────────────────────────────────────────────────────────┘
```

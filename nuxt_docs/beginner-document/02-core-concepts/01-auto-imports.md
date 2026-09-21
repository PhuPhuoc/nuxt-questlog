# Auto-Imports & Script Setup

> **Mục tiêu:** Hiểu cách Nuxt tự động import code và cách sử dụng `<script setup>`.

## Mục lục

1. [Auto-Imports là gì?](#1-auto-imports-là-gì)
2. [Những gì được auto-import?](#2-những-gì-được-auto-import)
3. [Script Setup là gì?](#3-script-setup-là-gì)
4. [Tự tạo auto-imports](#4-tự-tạo-auto-imports)
5. [Common Mistakes](#5-common-mistakes)

---

## 1. Auto-Imports Là Gì?

### So sánh Vue thuần và Nuxt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO-IMPORTS                                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE THUẦN (Manual imports):                                      │
│  ─────────────────────────────                                     │
│  <script>                                                          │
│    import { ref, computed } from 'vue'                          │
│    import MyComponent from './MyComponent.vue'                     │
│    import { useAuth } from '@/composables/useAuth'                │
│                                                                     │
│    const count = ref(0)                                          │
│  </script>                                                        │
│                                                                     │
│  NUXT (Auto-imports):                                            │
│  ──────────────────────────────                                    │
│  <script setup>                                                   │
│    // Không cần import!                                          │
│    const count = ref(0)          // ✅ Tự động có              │
│    <MyComponent />              // ✅ Tự động có              │
│    const auth = useAuth()       // ✅ Tự động có              │
│  </script>                                                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Tại sao Auto-Imports quan trọng?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LỢI ÍCH CỦA AUTO-IMPORTS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Code NGẮN HƠN                                                │
│     Không cần viết import statements dài dòng                    │
│                                                                     │
│  ✅ ÍT LỖI HƠN                                                   │
│     Không quên import, không sai tên                              │
│                                                                     │
│  ✅ CODE SẠCH HƠN                                                │
│     Tập trung vào logic thay vì imports                          │
│                                                                     │
│  ✅ ĐỒNG NHẤT                                                    │
│     Mọi người đều dùng cùng một cách                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Những Gì Được Auto-Import?

### 2.1 Vue APIs

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE APIs ĐƯỢC AUTO-IMPORT                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  REACTIVITY:                                                      │
│  ├── ref(), reactive(), computed()                               │
│  ├── watch(), watchEffect()                                      │
│  ├── toRef(), toRefs(), isRef()                                  │
│  └── shallowRef(), triggerRef()                                  │
│                                                                     │
│  LIFECYCLE HOOKS:                                                │
│  ├── onMounted(), onUnmounted()                                 │
│  ├── onBeforeMount(), onBeforeUnmount()                         │
│  ├── onUpdated(), onBeforeUpdate()                              │
│  └── onErrorCaptured()                                          │
│                                                                     │
│  COMPONENT UTILITIES:                                             │
│  ├── defineProps(), defineEmits()                               │
│  ├── defineExpose(), defineModel()                              │
│  └── useSlots(), useAttrs()                                     │
│                                                                     │
│  OTHER:                                                           │
│  ├── h() (createElement)                                        │
│  ├── nextTick()                                                  │
│  └── useCssModules()                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 Nuxt Composables

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT COMPOSABLES ĐƯỢC AUTO-IMPORT                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ROUTING:                                                          │
│  ├── useRoute()        → Lấy route hiện tại                    │
│  ├── useRouter()       → Navigate, etc.                         │
│  └── navigateTo()     → Redirect/forward                        │
│                                                                     │
│  DATA FETCHING:                                                   │
│  ├── useFetch()       → Fetch data đơn giản                    │
│  ├── useAsyncData()   → Fetch data với caching                 │
│  ├── useLazyFetch()   → Lazy version                           │
│  ├── useLazyAsyncData() → Lazy version                         │
│  └── $fetch()         → HTTP client                           │
│                                                                     │
│  STATE:                                                           │
│  ├── useState()       → Shared state (SSR-safe)                │
│  ├── useCookie()      → Cookie management                      │
│  └── useRequestHeaders() → Request headers                     │
│                                                                     │
│  META:                                                             │
│  ├── useHead()        → Set head tags                          │
│  ├── useSeoMeta()     → SEO meta tags                         │
│  └── useNuxtApp()     → Access nuxt app instance              │
│                                                                     │
│  ERROR:                                                           │
│  ├── createError()    → Throw error                           │
│  └── clearError()     → Clear error                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.3 Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPONENTS ĐƯỢC AUTO-IMPORT                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  BUILT-IN:                                                        │
│  ├── <NuxtPage />       → Render current page                   │
│  ├── <NuxtLink />       → Smart link with prefetching           │
│  ├── <NuxtLayout />     → Layout wrapper                        │
│  ├── <NuxtRouteAnnouncer /> → Accessibility                     │
│  └── <NuxtWelcome />    → Default welcome page                 │
│                                                                     │
│  CUSTOM (trong app/components/):                                 │
│  ├── Header.vue → <Header />                                    │
│  ├── Footer.vue → <Footer />                                    │
│  ├── ui/Button.vue → <UiButton />                               │
│  └── base/Card.vue → <BaseCard />                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.4 Utils (app/utils/)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    UTILITIES ĐƯỢC AUTO-IMPORT                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Bất kỳ function nào export từ app/utils/ sẽ được auto-import  │
│                                                                     │
│  Ví dụ:                                                          │
│  ├── app/utils/formatDate.ts → formatDate()                    │
│  ├── app/utils/validateEmail.ts → validateEmail()              │
│  └── app/utils/constants.ts → CONSTANTS                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Script Setup Là Gì?

### 3.1 `<script setup>` vs `<script>`

```vue
<!-- ❌ CÁCH CŨ: <script> thông thường -->
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      count.value++
    }

    // Phải return để dùng trong template
    return { count, increment }
  }
}
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

```vue
<!-- ✅ CÁCH MỚI: <script setup> -->
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}

// KHÔNG CẦN return!
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

### 3.2 Lợi ích của `<script setup>`

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LỢI ÍCH CỦA <script setup>                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. CODE NGẮN HƠN                                                 │
│     Không cần return object                                        │
│                                                                     │
│  2. PROPS VÀ EMITS ĐƠN GIẢN HƠN                                  │
│     defineProps() và defineEmits() là compiler macros             │
│                                                                     │
│  3. TYPE SCRIPT TỐT HƠN                                          │
│     Type inference tự động                                        │
│                                                                     │
│  4. PERFORMANCE TỐT HƠN                                           │
│     Code chạy như setup() function, hiệu quả hơn                 │
│                                                                     │
│  5. ĐƯỢC KHUYẾN NGHỊ TRONG NUXT                                 │
│     Nuxt sử dụng <script setup> làm mặc định                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.3 Ví dụ đầy đủ với Props và Emits

```vue
<!-- UserCard.vue -->
<script setup lang="ts">
// Props với TypeScript
const props = defineProps<{
  name: string
  email: string
  avatar?: string
  role?: 'admin' | 'user' | 'guest'
}>()

// Emits
const emit = defineEmits<{
  click: [event: MouseEvent]
  edit: [id: string]
}>()

// Reactive state
const isExpanded = ref(false)

// Computed
const initials = computed(() => {
  return props.name
    .split(' ')
    .map(n => n[0])
    .join('')
    .toUpperCase()
})

// Methods
function toggleExpand() {
  isExpanded.value = !isExpanded.value
}

function handleClick() {
  emit('click', new MouseEvent('click'))
}

function handleEdit() {
  emit('edit', props.email)
}
</script>

<template>
  <div class="user-card" :class="{ expanded: isExpanded }">
    <img
      :src="avatar || `https://ui-avatars.com/api/?name=${name}`"
      :alt="name"
      class="avatar"
    />

    <div class="info">
      <h3>{{ name }}</h3>
      <p>{{ email }}</p>
      <span v-if="role" :class="['badge', `badge-${role}`]">
        {{ role }}
      </span>
    </div>

    <div class="actions">
      <button @click="handleClick">View</button>
      <button @click="handleEdit">Edit</button>
    </div>
  </div>
</template>
```

### 3.4 Props với Default Values

```vue
<script setup>
// Cách 1: Dùng withDefaults
const props = withDefaults(defineProps<{
  title: string
  count?: number
  items?: string[]
}>(), {
  count: 0,
  items: () => []
})

// Cách 2: Dùng defineProps với defaults
const props = defineProps({
  title: { type: String, required: true },
  count: { type: Number, default: 0 },
  items: { type: Array, default: () => [] }
})
</script>
```

---

## 4. Tự Tạo Auto-Imports

### 4.1 Tạo Composable

```typescript
// app/composables/useCounter.ts
export const useCounter = (initialValue = 0) => {
  const count = ref(initialValue)

  function increment() {
    count.value++
  }

  function decrement() {
    count.value--
  }

  function reset() {
    count.value = initialValue
  }

  return {
    count: readonly(count),  // readonly để không modify từ bên ngoài
    increment,
    decrement,
    reset
  }
}
```

```vue
<!-- Sử dụng - KHÔNG cần import! -->
<script setup>
const { count, increment, decrement } = useCounter(10)
</script>
```

### 4.2 Tạo Utility Function

```typescript
// app/utils/formatDate.ts
export const formatDate = (date: Date | string, locale = 'vi-VN') => {
  const d = typeof date === 'string' ? new Date(date) : date
  return new Intl.DateTimeFormat(locale, {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(d)
}

export const formatCurrency = (amount: number, currency = 'VND') => {
  return new Intl.NumberFormat('vi-VN', {
    style: 'currency',
    currency
  }).format(amount)
}

export const formatNumber = (num: number) => {
  return new Intl.NumberFormat('vi-VN').format(num)
}
```

```vue
<!-- Sử dụng - KHÔNG cần import! -->
<script setup>
const date = '2024-01-15'
const price = 150000

// Tự động có sẵn!
console.log(formatDate(date))        // "15 tháng 1, 2024"
console.log(formatCurrency(price))  // "150.000 ₫"
console.log(formatNumber(1000000))  // "1.000.000"
</script>
```

### 4.3 Tạo Constants

```typescript
// app/utils/constants.ts
export const APP_NAME = 'BlogTalk'
export const APP_VERSION = '1.0.0'

export const ROUTES = {
  HOME: '/',
  BLOG: '/blog',
  ABOUT: '/about',
  CONTACT: '/contact'
} as const

export const HTTP_STATUS = {
  OK: 200,
  CREATED: 201,
  BAD_REQUEST: 400,
  UNAUTHORIZED: 401,
  NOT_FOUND: 404,
  SERVER_ERROR: 500
} as const
```

```vue
<!-- Sử dụng -->
<script setup>
console.log(APP_NAME)        // "BlogTalk"
console.log(ROUTES.HOME)    // "/"
console.log(HTTP_STATUS.OK)  // 200
</script>
```

---

## 5. Common Mistakes

### ❌ Sai: Quên auto-import hoạt động như thế nào

```vue
<!-- ❌ SAI - Vẫn phải import từ thư viện bên ngoài -->
<script setup>
import { ref } from 'vue'  // Vue APIs thì OK
import { useAuth } from '@/composables/useAuth'  // ❌ Sai - Nếu trong app/composables/
</script>
```

```vue
<!-- ✅ ĐÚNG - Composable trong app/composables/ tự động có -->
<script setup>
// Vue APIs - Cần import từ 'vue'
import { ref } from 'vue'

// Composable trong app/composables/ - KHÔNG cần import!
const auth = useAuth()  // ✅ Tự động có

// Utils trong app/utils/ - KHÔNG cần import!
const date = formatDate(new Date())  // ✅ Tự động có
</script>
```

### ❌ Sai: Composable không có prefix "use"

```typescript
// ❌ SAI - Không auto-import được
// app/composables/auth.ts
export const auth = () => {
  // ...
}
```

```typescript
// ✅ ĐÚNG - Phải có prefix "use"
// app/composables/useAuth.ts
export const useAuth = () => {
  // ...
}
```

### ❌ Sai: Store không có suffix "Store"

```typescript
// ❌ SAI - Không auto-import được
// app/stores/auth.ts
export const useAuth = defineStore('auth', () => {
  // ...
})
```

```typescript
// ✅ ĐÚNG - Phải có suffix "Store"
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // ...
})
```

### ❌ Sai: Import từ sai thư mục

```vue
<!-- ❌ SAI -->
<script setup>
import { formatDate } from '@/utils/formatDate'
</script>
```

```vue
<!-- ✅ ĐÚNG - KHÔNG cần import! -->
<script setup>
// Tự động có từ app/utils/formatDate.ts
const date = formatDate(new Date())
</script>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO-IMPORTS CHEAT SHEET                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ ĐƯỢC AUTO-IMPORT TỰ ĐỘNG:                                    │
│  ────────────────────────────────────────────────────────          │
│  • Vue APIs: ref, computed, watch, onMounted, etc.                 │
│  • Nuxt composables: useRoute, useFetch, useState, etc.            │
│  • Components trong app/components/                                │
│  • Functions trong app/utils/                                      │
│  • Composables trong app/composables/ (phải có prefix "use")       │
│  • Stores trong app/stores/ (phải có suffix "Store")              │
│                                                                     │
│  ❌ CẦN IMPORT THỦ CÔNG:                                         │
│  ────────────────────────────────────────────────────────           │
│  • Thư viện bên ngoài (lodash, axios, etc.)                        │
│  • Vue APIs từ 'vue' (ref, computed, etc.)                         │
│  • Hàm từ thư viện                                                │
│                                                                     │
│  📁 CẤU TRÚC:                                                     │
│  ────────────────────────────────────────────────────────          │
│  app/composables/useAuth.ts  → useAuth()                           │
│  app/utils/formatDate.ts     → formatDate()                        │
│  app/components/Header.vue   → <Header />                          │
│  app/stores/auth.ts         → useAuthStore()                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [02-components.md](02-components.md) - Components và Props

hoặc → [03-routing-basics.md](03-routing-basics.md) - Routing cơ bản

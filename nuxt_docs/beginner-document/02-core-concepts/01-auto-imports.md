# Auto-Imports & Script Setup

> **Mục tiêu:** Hiểu cách Nuxt tự động import code và cách sử dụng `<script setup>` trong Nuxt 4.

## Mục lục

1. [Auto-Imports là gì?](#1-auto-imports-là-gì)
2. [Những gì được auto-import?](#2-những-gì-được-auto-import)
3. [Script Setup là gì?](#3-script-setup-là-gì)
4. [Tự tạo auto-imports](#4-tự-tạo-auto-imports)
5. [Common Mistakes](#5-common-mistakes)

---

## 1. Auto-Imports Là Gì?

### 1.1 Dùng để làm gì?

**Auto-Imports giúp bạn KHÔNG CẦN viết `import` thủ công!**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SO SÁNH: MANUAL vs AUTO-IMPORTS                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE THUẦN (Manual imports):                                      │
│  ─────────────────────────────                                      │
│  <script setup>                                                    │
│    import { ref, computed } from 'vue'                          │
│    import MyComponent from './MyComponent.vue'                     │
│    import { useAuth } from '@/composables/useAuth'                │
│                                                                     │
│    const count = ref(0)                                          │
│  </script>                                                        │
│                                                                     │
│  NUXT (Auto-imports):                                            │
│  ──────────────────────────────                                     │
│  <script setup lang="ts">                                         │
│    // Không cần import!                                           │
│    const count = ref(0)          // ✅ Tự động có              │
│    <MyComponent />              // ✅ Tự động có              │
│    const auth = useAuth()       // ✅ Tự động có              │
│  </script>                                                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Có Sẵn Hay Cần Custom?

**ĐÂY LÀ TÍNH NĂNG CÓ SẴN CỦA NUXT 4!**

- Nuxt tự động scan và import:
  - Vue APIs (`ref`, `computed`, `watch`, etc.)
  - Nuxt composables (`useRoute`, `useFetch`, etc.)
  - Components trong `app/components/`
  - Functions trong `app/utils/`
  - Composables trong `app/composables/`

### 1.3 Cơ Chế Hoạt Động - Behind The Scenes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT AUTO-IMPORT HOẠT ĐỘNG NHƯ THẾ NÀO?        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. NUXT SCAN DIRECTORIES                                        │
│     ├── app/components/  → Scan components                        │
│     ├── app/composables/ → Scan composables                        │
│     └── app/utils/      → Scan utilities                          │
│                                                                     │
│  2. NUXT GENERATE IMPORTS                                        │
│     Tạo file auto-imports:                                        │
│     └── .nuxt/auto-imports.d.ts                                   │
│                                                                     │
│  3. VITE BUNDLE                                                  │
│     Khi build, Vite resolve imports từ đây                      │
│     Không cần import thủ công!                                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.4 Lợi Ích

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LỢI ÍCH CỦA AUTO-IMPORTS                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ CODE NGẮN HƠN                                                │
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
│  ├── shallowRef(), triggerRef(), customRef()                    │
│  ├── readonly(), shallowReactive(), shallowReadonly()           │
│  └── markRaw(), toRaw()                                          │
│                                                                     │
│  LIFECYCLE HOOKS:                                                │
│  ├── onMounted(), onUnmounted()                                 │
│  ├── onBeforeMount(), onBeforeUnmount()                         │
│  ├── onUpdated(), onBeforeUpdate()                              │
│  ├── onActivated(), onDeactivated()                             │
│  └── onErrorCaptured()                                          │
│                                                                     │
│  COMPONENT UTILITIES:                                             │
│  ├── defineProps(), defineEmits()                               │
│  ├── defineExpose(), defineModel()                              │
│  ├── defineSlots()                                              │
│  ├── useSlots(), useAttrs()                                     │
│  └── useTemplateRef()                                           │
│                                                                     │
│  OTHER:                                                           │
│  ├── h() (createElement)                                        │
│  ├── nextTick()                                                  │
│  ├── useTransition()                                            │
│  └── useId()                                                    │
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
│  ├── useNuxtApp()     → Access nuxt app instance              │
│  └── useRuntimeConfig() → Runtime config                       │
│                                                                     │
│  ERROR:                                                           │
│  ├── createError()    → Throw error                           │
│  ├── showError()      → Show error page                       │
│  └── clearError()     → Clear error                           │
│                                                                     │
│  PAGE:                                                            │
│  ├── definePageMeta() → Page metadata                          │
│  └── usePageMeta()    → Page metadata reactive                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.3 Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPONENTS ĐƯỢC AUTO-IMPORT                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  BUILT-IN (Nuxt cung cấp sẵn):                                │
│  ├── <NuxtPage />       → Render current page                   │
│  ├── <NuxtLink />       → Smart link with prefetching           │
│  ├── <NuxtLayout />     → Layout wrapper                        │
│  ├── <NuxtRouteAnnouncer /> → Accessibility                     │
│  ├── <NuxtLoadingIndicator /> → Loading indicator              │
│  ├── <NuxtApp />        → App wrapper (advanced)              │
│  └── <NuxtWelcome />    → Default welcome page                 │
│                                                                     │
│  CUSTOM (trong app/components/):                                 │
│  ├── Header.vue → <Header />                                    │
│  ├── Footer.vue → <Footer />                                    │
│  ├── ui/Button.vue → <UiButton />                               │
│  └── base/Card.vue → <BaseCard />                               │
│                                                                     │
│  LAZY (prefix "Lazy"):                                           │
│  ├── <LazyModal />    → Tải khi cần                           │
│  └── <LazyChart />    → Tải khi cần                           │
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
│  └── app/utils/constants.ts → APP_NAME, CONFIG                    │
│                                                                     │
│  ⚠️ LƯU Ý:                                                     │
│  └── Composables phải có prefix "use"                           │
│  └── Stores phải có suffix "Store"                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Script Setup Là Gì?

### 3.1 Dùng để làm gì?

`<script setup>` là syntax đặc biệt của Vue 3 giúp viết component ngắn gọn hơn.

```vue
<!-- ❌ CÁCH CŨ: <script> thông thường -->
<script setup lang="ts">
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
<!-- ✅ CÁCH MỚI: <script setup> - KHÔNG cần import Vue APIs! -->
<script setup lang="ts">
// ref, computed, onMounted... đều được auto-import!
const count = ref(0)

function increment() {
  count.value++
}

// KHÔNG CẦN return! - Tự động expose cho template
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

### 3.2 Có Sẵn Hay Cần Custom?

**ĐÂY LÀ TÍNH NĂNG CÓ SẴN CỦA VUE 3 + NUXT 4!**

- `<script setup>` là compiler-level syntax
- Không cần config gì thêm
- TypeScript được hỗ trợ ngay

### 3.3 Cơ Chế Hoạt Động - Behind The Scenes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    <script setup> HOẠT ĐỘNG NHƯ THẾ NÀO?          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. VUE COMPILER CHUYỂN ĐỔI                                     │
│     <script setup>                                                │
│     → Chuyển thành setup() function                             │
│                                                                     │
│  2. AUTO RETURN                                                  │
│     Tất cả top-level variables tự động expose cho template     │
│                                                                     │
│  3. COMPILE-TIME MACROS                                           │
│     defineProps() → Props declaration                             │
│     defineEmits() → Emits declaration                            │
│     defineExpose() → Exposed properties                           │
│                                                                     │
│  4. PERFORMANCE TỐT HƠN                                         │
│     Code chạy như setup() function, hiệu quả hơn               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.4 Ví dụ đầy đủ với TypeScript

```vue
<!-- app/components/UserCard.vue -->
<script setup lang="ts">
// Props - TypeScript generic
const props = defineProps<{
  name: string
  email: string
  avatar?: string
  role: 'admin' | 'user' | 'guest'
}>()

// Emits - TypeScript generic
const emit = defineEmits<{
  click: [event: MouseEvent]
  edit: [userId: string]
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

// KHÔNG CẦN RETURN! - Tự động expose cho template
</script>

<template>
  <div class="user-card" :class="{ expanded: isExpanded }">
    <img
      :src="props.avatar || `https://ui-avatars.com/api/?name=${props.name}`"
      :alt="props.name"
      class="avatar"
    />

    <div class="info">
      <h3>{{ props.name }}</h3>
      <p>{{ props.email }}</p>
      <span :class="['badge', `badge-${props.role}`]">
        {{ props.role }}
      </span>
    </div>

    <div class="actions">
      <button @click="handleClick">View</button>
      <button @click="handleEdit">Edit</button>
    </div>
  </div>
</template>
```

### 3.5 Props với Default Values

```ts
<script setup lang="ts">
// Cách 1: withDefaults (Khuyến nghị)
const props = withDefaults(defineProps<{
  title: string
  count?: number
  items?: string[]
}>(), {
  count: 0,
  items: () => []  // Array/Object phải dùng factory function
})
</script>
```

---

## 4. Tự Tạo Auto-Imports

### 4.1 Tạo Composable

**Quy tắc: File phải có prefix "use" để được auto-import!**

```ts
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
<script setup lang="ts">
const { count, increment, decrement } = useCounter(10)
</script>
```

### 4.2 Tạo Utility Function

```ts
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
<script setup lang="ts">
const date = '2024-01-15'
const price = 150000

// Tự động có sẵn!
console.log(formatDate(date))        // "15 tháng 1, 2024"
console.log(formatCurrency(price))  // "150.000 ₫"
console.log(formatNumber(1000000))  // "1.000.000"
</script>
```

### 4.3 Tạo Constants

```ts
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
<script setup lang="ts">
console.log(APP_NAME)        // "BlogTalk"
console.log(ROUTES.HOME)    // "/"
console.log(HTTP_STATUS.OK)  // 200
</script>
```

### 4.4 Tạo Store

**Quy tắc: Function phải có suffix "Store" để được auto-import!**

```ts
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)
  const token = useCookie('auth_token')

  const isLoggedIn = computed(() => !!token.value && !!user.value)

  async function login(email: string, password: string) {
    // ... login logic
  }

  return { user, token, isLoggedIn, login }
})
```

```vue
<!-- Sử dụng - KHÔNG cần import! -->
<script setup lang="ts">
const auth = useAuthStore()
console.log(auth.isLoggedIn)
</script>
```

---

## 5. Common Mistakes

### ❌ Sai: Import Vue APIs trong Nuxt

```vue
<!-- ❌ SAI - Không cần import Vue APIs trong Nuxt -->
<script setup lang="ts">
import { ref } from 'vue'  // ❌ Không cần!
</script>
```

```vue
<!-- ✅ ĐÚNG - Vue APIs được auto-import -->
<script setup lang="ts">
const count = ref(0)  // ✅ Tự động có
</script>
```

### ❌ Sai: Composable không có prefix "use"

```ts
// ❌ SAI - Không auto-import được
// app/composables/auth.ts
export const auth = () => {
  // ...
}
```

```ts
// ✅ ĐÚNG - Phải có prefix "use"
// app/composables/useAuth.ts
export const useAuth = () => {
  // ...
}
```

### ❌ Sai: Store không có suffix "Store"

```ts
// ❌ SAI - Không auto-import được
// app/stores/auth.ts
export const useAuth = defineStore('auth', () => {
  // ...
})
```

```ts
// ✅ ĐÚNG - Phải có suffix "Store"
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // ...
})
```

### ❌ Sai: Import từ sai thư mục

```vue
<!-- ❌ SAI -->
<script setup lang="ts">
import { formatDate } from '@/utils/formatDate'  // ❌ Không cần!
</script>
```

```vue
<!-- ✅ ĐÚNG - KHÔNG cần import! -->
<script setup lang="ts">
// Tự động có từ app/utils/formatDate.ts
const date = formatDate(new Date())  // ✅ Tự động có
</script>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO-IMPORTS CHEAT SHEET                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ ĐƯỢC AUTO-IMPORT TỰ ĐỘNG:                                    │
│  ────────────────────────────────────────────────────────           │
│  • Vue APIs: ref, computed, watch, onMounted, etc.                 │
│  • Nuxt composables: useRoute, useFetch, useState, etc.            │
│  • Components trong app/components/                                │
│  • Functions trong app/utils/                                      │
│  • Composables trong app/composables/ (phải có prefix "use")      │
│  • Stores trong app/stores/ (phải có suffix "Store")              │
│                                                                     │
│  ❌ CẦN IMPORT THỦ CÔNG:                                         │
│  ────────────────────────────────────────────────────────           │
│  • Thư viện bên ngoài (lodash, axios, dayjs, etc.)                 │
│  • Hàm từ thư viện bên ngoài                                      │
│                                                                     │
│  📁 CẤU TRÚC:                                                     │
│  ────────────────────────────────────────────────────────           │
│  app/composables/useAuth.ts  → useAuth()                           │
│  app/utils/formatDate.ts     → formatDate()                        │
│  app/components/Header.vue   → <Header />                          │
│  app/stores/auth.ts         → useAuthStore()                       │
│                                                                     │
│  <script setup>:                                                   │
│  ────────────────────────────────────────────────────────           │
│  • Không cần return - tự động expose cho template                 │
│  • defineProps<{...}>() - khai báo props                           │
│  • defineEmits<{...}>() - khai báo emits                          │
│  • withDefaults(defineProps<{...}>(), {}) - default values         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [02-components.md](02-components.md) - Components và Props

hoặc → [03-routing-basics.md](03-routing-basics.md) - Routing cơ bản

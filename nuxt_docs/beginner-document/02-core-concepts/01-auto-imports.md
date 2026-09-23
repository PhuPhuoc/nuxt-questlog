# Auto-Imports - Hệ Thống Tự Động Import

> **Mục tiêu:** Hiểu cách Nuxt tự động import Vue APIs, composables, và utilities.

## Mục lục

1. [Auto-imports là gì?](#1-auto-imports-là-gì)
2. [Vue APIs được auto-import](#2-vue-apis-được-auto-import)
3. [Nuxt Composables được auto-import](#3-nuxt-composables-được-auto-import)
4. [Custom auto-imports](#4-custom-auto-imports)
5. [Mẹo và best practices](#5-mẹo-và-best-practices)

---

## 1. Auto-Imports là gì?

### 1.1 Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO-IMPORTS - GIẢI THÍCH                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TRADITIONAL (Vue thuần / Vite):                                   │
│  ─────────────────────────────────                                 │
│  import { ref, computed } from 'vue'      ← Phải import           │
│  import { useRoute } from 'vue-router'   ← Phải import           │
│                                                                     │
│  NUXT (Auto-imports):                                             │
│  ────────────────────────────                                       │
│  const count = ref(0)                     ← Không cần import!     │
│  const route = useRoute()                 ← Không cần import!     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Lợi ích

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LỢI ÍCH CỦA AUTO-IMPORTS                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Code ngắn hơn                                                │
│  ├── Không cần import thủ công                                   │
│  └── Không cần nhớ import paths                                   │
│                                                                     │
│  ✅ Giảm boilerplate                                             │
│  ├── Không cần `import { ref } from 'vue'`                       │
│  └── Không cần `import { useRoute } from 'vue-router'`           │
│                                                                     │
│  ✅ Nhất quán                                                    │
│  ├── Nuxt tự động import đúng phiên bản                          │
│  └── Tránh conflict giữa các versions                             │
│                                                                     │
│  ✅ TypeScript tốt                                               │
│  ├── Types được auto-generated                                    │
│  └── IntelliSense hoạt động tốt                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Vue APIs Được Auto-Import

### 2.1 Vue 3 Reactivity APIs

| API | Mô tả | Ví dụ |
|-----|-------|--------|
| `ref()` | Reactive ref | `const count = ref(0)` |
| `reactive()` | Reactive object | `const state = reactive({ count: 0 })` |
| `computed()` | Computed property | `const doubled = computed(() => count.value * 2)` |
| `watch()` | Watch for changes | `watch(count, (newVal) => console.log(newVal))` |
| `watchEffect()` | Auto-tracking watcher | `watchEffect(() => console.log(count.value))` |
| `watchPostEffect()` | Watch after DOM update | - |
| `watchSyncEffect()` | Sync watcher | - |

### 2.2 Vue 3 Lifecycle Hooks

| Hook | Mô tả |
|------|-------|
| `onMounted()` | Sau khi component mounted |
| `onUnmounted()` | Sau khi component unmounted |
| `onBeforeMount()` | Trước khi mount |
| `onBeforeUnmount()` | Trước khi unmount |
| `onUpdated()` | Sau khi re-render |
| `onBeforeUpdate()` | Trước khi re-render |
| `onErrorCaptured()` | Khi có lỗi được capture |

### 2.3 Vue 3 Component APIs

| API | Mô tả |
|-----|-------|
| `defineProps()` | Định nghĩa props (script setup) |
| `defineEmits()` | Định nghĩa emits (script setup) |
| `defineExpose()` | Định nghĩa exposed properties |
| `defineModel()` | Two-way binding (Vue 3.4+) |
| `defineOptions()` | Định nghĩa component options |
| `useSlots()` | Lấy slots |
| `useAttrs()` | Lấy attrs |

### 2.4 Ví dụ: Không cần import

```vue
<!-- app/pages/index.vue -->
<script setup lang="ts">
// ✅ ref - auto-imported
const count = ref(0)

// ✅ computed - auto-imported
const doubled = computed(() => count.value * 2)

// ✅ lifecycle - auto-imported
onMounted(() => {
  console.log('Component mounted!')
})

// ✅ watch - auto-imported
watch(count, (newVal) => {
  console.log('Count changed:', newVal)
})

// ✅ defineProps - auto-imported
const props = defineProps<{
  title: string
}>()

// ✅ defineEmits - auto-imported
const emit = defineEmits<{
  click: [event: MouseEvent]
}>()
</script>

<template>
  <div>{{ count }} x 2 = {{ doubled }}</div>
</template>
```

---

## 3. Nuxt Composables Được Auto-Import

### 3.1 Routing

| Composable | Mô tả | Ví dụ |
|------------|--------|--------|
| `useRoute()` | Lấy route hiện tại | `const route = useRoute()` |
| `useRouter()` | Lấy router instance | `const router = useRouter()` |
| `navigateTo()` | Navigate đến URL | `navigateTo('/about')` |
| `defineNuxtRouteTransformer()` | Transform route |
| `useLink()` | Navigate với active state |

### 3.2 Data Fetching

| Composable | Mô tả | Ví dụ |
|------------|--------|--------|
| `useFetch()` | Fetch data với SSR | `const { data } = await useFetch('/api/posts')` |
| `useAsyncData()` | Async data với SSR | `const { data } = await useAsyncData('key', () => fetch())` |
| `$fetch()` | HTTP client | `await $fetch('/api/users')` |
| `useLazyFetch()` | Lazy fetch | `useLazyFetch('/api/posts')` |
| `useLazyAsyncData()` | Lazy async data |

### 3.3 State Management

| Composable | Mô tả | Ví dụ |
|------------|--------|--------|
| `useState()` | Shared state (SSR-safe) | `const count = useState('count', () => 0)` |
| `useNuxtState()` | Nuxt state |
| `useCookie()` | Cookie state | `const token = useCookie('token')` |
| `useStorage()` | Local/async storage |

### 3.4 SEO

| Composable | Mô tả |
|------------|--------|
| `useSeoMeta()` | Set SEO meta |
| `useHead()` | Set head tags |
| `useHtmlAttrs()` | HTML attributes |
| `useBodyAttrs()` | Body attributes |
| `useTitle()` | Page title |
| `useMeta()` | Meta utilities |

### 3.5 Runtime Config

| Composable | Mô tả |
|------------|--------|
| `useRuntimeConfig()` | Runtime config |

### 3.6 App

| Composable | Mô tả |
|------------|--------|
| `useNuxtApp()` | Nuxt app instance |
| `useAppConfig()` | App config |
| `useError()` | Error handling |
| `useLogger()` | Logging |
| `useRequestHeaders()` | Request headers |
| `useRequestEvent()` | Request event |
| `useScheme()` | Color scheme |
| `useColorMode()` | Color mode |

### 3.7 Example: useRoute và useRouter

```vue
<!-- app/pages/blog/[slug].vue -->
<script setup lang="ts">
// ✅ useRoute - auto-imported
const route = useRoute()

// Lấy params từ URL
const slug = route.params.slug

// Lấy query string
const search = route.query.search

// ✅ useRouter - auto-imported
const router = useRouter()

function goBack() {
  router.back()
}

function goToPost(id: string) {
  router.push(`/blog/${id}`)
}
</script>

<template>
  <div>
    <h1>Post: {{ slug }}</h1>
    <p v-if="search">Search: {{ search }}</p>
    <button @click="goBack">Quay lại</button>
  </div>
</template>
```

### 3.8 Example: useFetch

```vue
<script setup lang="ts">
// ✅ useFetch - auto-imported
const { data: posts, pending, error } = await useFetch('/api/posts', {
  default: () => []
})
</script>

<template>
  <div>
    <div v-if="pending">Loading...</div>
    <div v-else-if="error">Error: {{ error.message }}</div>
    <div v-else>
      <article v-for="post in posts" :key="post.id">
        {{ post.title }}
      </article>
    </div>
  </div>
</template>
```

### 3.9 Example: useState

```typescript
// composables/useCounter.ts

// ✅ useState - auto-imported
export const useCounter = () => {
  // Shared state giữa components (SSR-safe)
  const count = useState('counter', () => 0)

  function increment() {
    count.value++
  }

  function decrement() {
    count.value--
  }

  return {
    count: readonly(count),
    increment,
    decrement
  }
}
```

```vue
<!-- component A -->
<script setup lang="ts">
const { count, increment } = useCounter()
</script>
<template>
  <button @click="increment">Count: {{ count }}</button>
</template>
```

```vue
<!-- component B - dùng cùng state -->
<script setup lang="ts">
const { count } = useCounter()
</script>
<template>
  <p>Count also: {{ count }}</p>
</template>
```

---

## 4. Custom Auto-Imports

### 4.1 Auto-import từ composables/

Files trong `app/composables/` được auto-imported:

```
app/composables/
├── useAuth.ts      → useAuth()
├── useTheme.ts     → useTheme()
└── useFetchUser.ts → useFetchUser()
```

```typescript
// app/composables/useAuth.ts
export const useAuth = () => {
  const user = ref(null)
  const isAuthenticated = computed(() => !!user.value)

  async function login(credentials: Credentials) {
    // Login logic
  }

  return {
    user: readonly(user),
    isAuthenticated,
    login
  }
}
```

```vue
<!-- Sử dụng - KHÔNG CẦN IMPORT! -->
<script setup lang="ts">
const { user, login } = useAuth()
</script>
```

### 4.2 Auto-import từ utils/

Files trong `app/utils/` được auto-imported:

```
app/utils/
├── formatDate.ts    → formatDate()
├── validateEmail.ts → validateEmail()
└── constants.ts    → các constants
```

```typescript
// app/utils/formatDate.ts
export function formatDate(date: Date | string): string {
  const d = new Date(date)
  return d.toLocaleDateString('vi-VN')
}
```

```vue
<!-- Sử dụng - KHÔNG CẦN IMPORT! -->
<script setup lang="ts">
const formatted = formatDate(new Date())  // ✅
</script>
```

### 4.3 Auto-import từ stores/

Files trong `app/stores/` được auto-imported:

```
app/stores/
├── auth.ts    → useAuthStore()
├── cart.ts   → useCartStore()
└── product.ts → useProductStore()
```

```typescript
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)
  const token = ref<string | null>(null)

  const isAuthenticated = computed(() => !!token.value)

  async function login(credentials: Credentials) {
    // Login logic
  }

  return { user, token, isAuthenticated, login }
})
```

```vue
<!-- Sử dụng - KHÔNG CẦN IMPORT! -->
<script setup lang="ts">
const authStore = useAuthStore()  // ✅
</script>
```

---

## 5. Mẹo và Best Practices

### 5.1 Có nên dùng auto-imports không?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO-IMPORTS - NÊN HAY KHÔNG?                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ NÊN DÙNG trong Nuxt:                                          │
│  ├── Vue APIs: ref, computed, watch, lifecycle hooks              │
│  ├── Nuxt composables: useRoute, useFetch, useState              │
│  ├── Custom composables trong composables/                        │
│  ├── Utilities trong utils/                                       │
│  └── Pinia stores trong stores/                                   │
│                                                                     │
│  ⚠️ VẪN CẦN IMPORT thủ công:                                    │
│  ├── Third-party libraries: axios, lodash                        │
│  ├── Vue plugins: vue-i18n, vue-router                            │
│  └── Type definitions                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Khi nào cần import thủ công?

```vue
<script setup lang="ts">
// ✅ Auto-imported: Vue APIs
const count = ref(0)
const doubled = computed(() => count.value * 2)

// ✅ Auto-imported: Nuxt composables
const route = useRoute()
const { data } = await useFetch('/api/posts')

// ⚠️ Vẫn cần import: Third-party libraries
import axios from 'axios'
import _ from 'lodash'

// ⚠️ Vẫn cần import: Vue globals không auto-imported
import { Transition, TransitionGroup } from 'vue'
</script>
```

### 5.3 Xem auto-imports được sinh ra

Nuxt tự động tạo file `.nuxt/auto-imports.d.ts`:

```bash
# Xem tất cả auto-imported functions
cat .nuxt/auto-imports.d.ts
```

```typescript
// Generated file (.nuxt/auto-imports.d.ts)
// Đây là file auto-generated - KHÔNG SỬA TAY

// Vue Reactivity APIs
declare function ref<T>(...): Ref<T>
declare function computed<T>(...): ComputedRef<T>
// ...

// Vue Lifecycle Hooks
declare function onMounted(...): void
declare function onUnmounted(...): void
// ...
```

### 5.4 Debug auto-imports

```vue
<script setup lang="ts">
// Khi không chắc chắn, có thể import thủ công để debug
// Sau đó xóa import để dùng auto-import

import { ref } from 'vue'  // Debug: xem có hoạt động không

// Nếu hoạt động, có thể xóa import vì auto-imported
</script>
```

### 5.5 Disable auto-imports cho một số trường hợp

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  imports: {
    // Disable auto-import cho một số APIs
    disabled: ['useAsyncData', 'useFetch'],
    
    // Hoặc tự định nghĩa imports
    presets: [
      {
        from: 'vue',
        imports: ['ref', 'computed', 'watch']
      }
    ]
  }
})
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTO-IMPORT CHEAT SHEET                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUE APIs (Auto-imported):                                        │
│  ├── ref, reactive, computed, watch, watchEffect                  │
│  ├── onMounted, onUnmounted, onUpdated, onBeforeUpdate            │
│  ├── defineProps, defineEmits, defineExpose                       │
│  └── useSlots, useAttrs                                           │
│                                                                     │
│  NUXT COMPOSABLES (Auto-imported):                                │
│  ├── useRoute, useRouter, navigateTo                               │
│  ├── useFetch, useAsyncData, $fetch                               │
│  ├── useState, useCookie, useStorage                              │
│  ├── useSeoMeta, useHead                                          │
│  └── useRuntimeConfig, useNuxtApp                                 │
│                                                                     │
│  CUSTOM (Auto-imported từ folders):                               │
│  ├── composables/*.ts → useXxx()                                  │
│  ├── utils/*.ts → xxx()                                           │
│  └── stores/*.ts → useXxxStore()                                  │
│                                                                     │
│  ⚠️ VẪN CẦN IMPORT:                                              │
│  ├── Third-party libraries (axios, lodash)                        │
│  └── Vue components từ npm                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [02-components.md](02-components.md) - Components & Props

hoặc → [03-routing-basics.md](03-routing-basics.md) - Routing cơ bản

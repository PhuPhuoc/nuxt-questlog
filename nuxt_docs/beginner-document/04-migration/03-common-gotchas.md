# Những Bẫy Thường Gặp

> **Mục tiêu:** Những lỗi phổ biến và cách tránh.

## Mục lục

1. [Component Naming](#1-component-naming)
2. [Auto-import Gotchas](#2-auto-import-gotchas)
3. [SSR Gotchas](#3-ssr-gotchas)
4. [Pinia Gotchas](#4-pinia-gotchas)
5. [TypeScript Gotchas](#5-typescript-gotchas)

---

## 1. Component Naming

### ❌ Sai: kebab-case

```
components/
├── my-component.vue     ❌
├── myComponent.vue      ❌
└── my_component.vue    ❌
```

### ✅ Đúng: PascalCase

```
components/
├── MyComponent.vue      ✅
├── BaseButton.vue       ✅
└── ui/
    └── Modal.vue       ✅ → <UiModal>
```

### ⚠️ Lazy Components

```vue
<!-- Prefix Lazy để lazy-load -->
<LazyModal v-if="show" />
<LazyHeavyChart v-if="showChart" />
```

---

## 2. Auto-import Gotchas

### ❌ Composable không có prefix "use"

```typescript
// ❌ Sai - Không auto-import được
export const auth = () => { ... }

// ✅ Đúng
export const useAuth = () => { ... }
```

### ❌ Store không có suffix "Store"

```typescript
// ❌ Sai - Không auto-import được
export const useAuth = defineStore('auth', () => { ... })

// ✅ Đúng
export const useAuthStore = defineStore('auth', () => { ... })
```

### ❌ Import từ sai path

```typescript
// ❌ Sai
import { useAuth } from '~/stores/auth'

// ✅ Đúng - Nuxt auto-imports
const authStore = useAuthStore()
```

### ✅ Quan sát auto-imports

```bash
# Xem file auto-imports trong .nuxt/
cat .nuxt/auto-imports.d.ts
```

---

## 3. SSR Gotchas

### ❌ Dùng browser APIs trên server

```typescript
// ❌ Sai - Lỗi trên server
if (localStorage.getItem('token')) {
  // ...
}
```

### ✅ Kiểm tra environment

```typescript
// ✅ Đúng - Chỉ chạy trên client
if (import.meta.client) {
  const token = localStorage.getItem('token')
}

// ✅ Hoặc dùng useCookie
const token = useCookie('token')
```

### ❌ Random/Date trong reactive state

```typescript
// ❌ Sai - Khác nhau server/client
const id = Math.random()
const now = new Date()
```

### ✅ SSR-safe values

```typescript
// ✅ Đúng - Khởi tạo sau mount
const now = ref('')

onMounted(() => {
  now.value = new Date().toISOString()
})
```

---

## 4. Pinia Gotchas

### ❌ Destructure store không dùng storeToRefs

```typescript
// ❌ Sai - Mất reactivity
const { user } = useAuthStore()
```

### ✅ Dùng storeToRefs

```typescript
// ✅ Đúng - Giữ reactivity
import { storeToRefs } from 'pinia'
const { user } = storeToRefs(useAuthStore())
```

### ❌ Gọi store trong setup context

```typescript
// ❌ Sai - Store chưa available
const authStore = useAuthStore() // Nếu gọi trước Pinia init
```

### ✅ Gọi trong setup/composable

```typescript
// ✅ Đúng
export const useAuth = () => {
  const authStore = useAuthStore()
  return { ... }
}
```

---

## 5. TypeScript Gotchas

### ❌ Import sai kiểu

```typescript
// ❌ Sai
import type { User } from './types'
```

### ✅ Import type đúng cách

```typescript
// ✅ Đúng
import type { User } from './types'

// Hoặc inline
type User = { name: string; age: number }
```

### ❌ Missing defineProps types

```typescript
// ❌ Sai - Không có type
defineProps({
  name: String
})
```

### ✅ Dùng TypeScript

```typescript
// ✅ Đúng
const props = defineProps<{
  name: string
  age?: number
}>()
```

---

## 🎯 Checklist

```
□ Component names là PascalCase?
□ Composables bắt đầu với "use"?
□ Stores kết thúc với "Store"?
□ Không dùng localStorage trực tiếp?
□ Dùng storeToRefs() khi destructure?
□ Dùng TypeScript cho props?
```

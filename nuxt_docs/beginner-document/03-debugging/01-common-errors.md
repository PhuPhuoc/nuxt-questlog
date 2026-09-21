# Lỗi Thường Gặp và Cách Fix

> **Mục tiêu:** Khi bạn gặp lỗi, đây là nơi đầu tiên để tìm giải pháp.

## Mục lục

1. [Lỗi "Component not found"](#1-lỗi-component-not-found)
2. [Lỗi "is called outside of setup"](#2-lỗi-is-called-outside-of-setup)
3. [Lỗi Hydration](#3-lỗi-hydration)
4. [Lỗi TypeScript](#4-lỗi-typescript)
5. [Lỗi Routing](#5-lỗi-routing)
6. [Lỗi Pinia](#6-lỗi-pinia)
7. [Lỗi Build](#7-lỗi-build)

---

## 1. Lỗi "Component not found"

### Triệu chứng

```
[Vue warn]: Failed to resolve component: MyComponent
```

### Nguyên nhân và cách fix

#### Nguyên nhân 1: Tên file không đúng

```
❌ Sai:  myComponent.vue  → <Mycomponent>
✅ Đúng: MyComponent.vue  → <MyComponent>
```

**Fix:**
```bash
# Đổi tên file thành PascalCase
# my-component.vue → MyComponent.vue
```

#### Nguyên nhân 2: File không trong thư mục components

```
❌ Sai: components/MyComponent.vue (nhưng đang ở root)
✅ Đúng: app/components/MyComponent.vue
```

**Fix:**
```bash
# Di chuyển vào đúng thư mục
mv components/ app/components/
```

#### Nguyên nhân 3: Component trong thư mục con

```
components/
└── ui/
    └── Modal.vue  → <UiModal>  ❌ SAI!

# Phải là: components/ui/Modal.vue → <UiModal>
```

**Fix:**
```bash
# Kiểm tra cấu trúc
components/
└── ui/
    └── Modal.vue  ✅ Đúng!
```

#### Nguyên nhân 4: Nuxt chưa nhận diện component mới

```bash
# Restart dev server
# Nhấn Ctrl+C để stop
# Sau đó chạy lại
bun run dev
```

### Checklist cho lỗi này

```
□ Tên file là PascalCase? (MyComponent.vue)
□ File trong app/components/?
□ Tên component đúng? (<MyComponent> không phải <Mycomponent>)
□ Đã restart dev server?
```

---

## 2. Lỗi "is called outside of setup"

### Triệu chứng

```
Error: useRoute() is called outside of setup()
Error: useRouter() is called outside of setup()
Error: useAuthStore() is called outside of setup()
```

### Nguyên nhân

Composables (như `useRoute()`, `useRouter()`, Pinia stores) phải được gọi **bên trong setup()** hoặc **lifecycle hooks**.

```vue
<script setup>
// ✅ ĐÚNG - Gọi trong setup
const route = useRoute()
</script>
```

```vue
<script>
// ❌ SAI - Gọi ngoài setup
const route = useRoute()  // LỖI!

export default {
  mounted() {
    const route = useRoute()  // Cũng LỖI!
  }
}
</script>
```

### Cách fix

#### Fix 1: Gọi trong setup (cho Vue 3 Composition API)

```vue
<script setup>
import { onMounted } from 'vue'

// ✅ Gọi trong setup
const route = useRoute()
const router = useRouter()
const authStore = useAuthStore()

// ✅ Hoặc trong lifecycle hooks
onMounted(() => {
  console.log(route.params.id)
})
</script>
```

#### Fix 2: Dùng watch thay vì lifecycle

```vue
<script setup>
// ✅ ĐÚNG - watchEffect chạy trong setup context
import { watchEffect } from 'vue'

const route = useRoute()

// Chạy khi route thay đổi
watchEffect(() => {
  console.log(route.params.id)
})
</script>
```

#### Fix 3: Dùng defineNuxtRouteTransformer (cho middleware)

```typescript
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // ✅ ĐƯỢC PHÉP - Middleware có setup context riêng
  const authStore = useAuthStore()

  if (!authStore.isLoggedIn) {
    return navigateTo('/login')
  }
})
```

### Nguyên nhân phổ biến khác

#### Composable được gọi trong callback

```vue
<script setup>
// ❌ SAI
setTimeout(() => {
  const route = useRoute()  // LỖI!
}, 1000)

// ❌ SAI
button.onclick = () => {
  const route = useRoute()  // LỖI!
}
</script>
```

```vue
<script setup>
// ✅ ĐÚNG
const route = useRoute()  // Gọi ở đây

setTimeout(() => {
  console.log(route.params.id)  // Dùng ở đây
}, 1000)
</script>
```

---

## 3. Lỗi Hydration

### Triệu chứng

```
[Vue warn]: Hydration text content mismatch...
[Vue warn]: Hydration class mismatch...
[Vue warn]: Hydration completed but there were warnings.
```

### Nguyên nhân

Hydration xảy ra khi **server render HTML** và **client re-render**. Lỗi này = Server và Client tạo ra HTML khác nhau.

### Các nguyên nhân phổ biến

#### Nguyên nhân 1: Dùng Date/Time

```vue
<script setup>
// ❌ SAI - Mỗi lần chạy tạo thời gian khác nhau
const now = new Date().toISOString()

// ❌ SAI - Math.random() tạo số khác nhau mỗi lần
const randomId = Math.random()
</script>
```

```vue
<script setup>
// ✅ ĐÚNG - Chạy trên client only
const now = ref('')

onMounted(() => {
  now.value = new Date().toISOString()  // Chạy sau hydration
})
</script>
```

#### Nguyên nhân 2: localStorage

```vue
<script setup>
// ❌ SAI - localStorage không có trên server
const token = localStorage.getItem('token')
```

```vue
<script setup>
// ✅ ĐÚNG - Dùng useCookie() thay vì localStorage
const token = useCookie('token')
```

```vue
<script setup>
// ✅ HOẶC - Chỉ chạy trên client
const token = ref(null)

onMounted(() => {
  token.value = localStorage.getItem('token')
})
</script>
```

#### Nguyên nhân 3: Dùng window/document

```vue
<script setup>
// ❌ SAI - window không có trên server
const width = window.innerWidth
```

```vue
<script setup>
// ✅ ĐÚNG - Kiểm tra environment
const isClient = import.meta.client

onMounted(() => {
  if (import.meta.client) {
    console.log(window.innerWidth)
  }
})
</script>
```

#### Nguyên nhân 4: Browser-only APIs

```vue
<script setup>
// ❌ SAI
const isOnline = navigator.onLine
```

```vue
<script setup>
// ✅ ĐÚNG
const isOnline = ref(true)

onMounted(() => {
  isOnline.value = navigator.onLine
})

// Lắng nghe sự kiện
window.addEventListener('online', () => isOnline.value = true)
window.addEventListener('offline', () => isOnline.value = false)
</script>
```

### Checklist Hydration

```
□ Có dùng Date.now() hoặc new Date()?
□ Có dùng Math.random()?
□ Có dùng localStorage trực tiếp?
□ Có dùng window/document?
□ Có dùng navigator.onLine?
□ Giá trị có thể khác nhau giữa server và client?
```

---

## 4. Lỗi TypeScript

### Lỗi: Cannot find type definition

```
TS6307: File '...' is listed in the tsconfig, but does not exist.
```

**Fix:**
```bash
# Chạy nuxt prepare
bun run dev
# Hoặc
npx nuxi prepare
```

### Lỗi: Property does not exist on type

```vue
<script setup lang="ts">
// ❌ LỖI
defineProps({
  name: String
})

console.log(props.names)  // "names" không tồn tại
</script>
```

**Fix:**
```typescript
// ✅ ĐÚNG
const props = defineProps<{
  name: string
}>()

console.log(props.name)  // Đúng
```

### Lỗi: Argument of type 'X' is not assignable

```typescript
// ❌ LỖI
const count: Ref<number> = ref('hello')  // string không assign được cho number
```

**Fix:**
```typescript
// ✅ ĐÚNG
const count: Ref<number> = ref(0)
const name: Ref<string> = ref('hello')
```

---

## 5. Lỗi Routing

### Lỗi: Page not found (404)

```
Cannot find any route matching path: /unknown
```

**Nguyên nhân:**
- Route chưa được tạo
- File name không đúng format

**Fix:**
```
Kiểm tra file trong pages/:
□ pages/about.vue → /about ✅
□ pages/blog/[slug].vue → /blog/:slug ✅
□ pages/[...catchall].vue → /* ✅
```

### Lỗi: Route params là undefined

```vue
<script setup>
// ❌ LỖI
const { id } = route.params
```

**Fix:**
```vue
<script setup>
// ✅ ĐÚNG
const route = useRoute()
const id = route.params.id  // Lấy từ params object
console.log(id)
</script>
```

### Lỗi: Middleware không chạy

```vue
<script setup>
// ❌ Đặt middleware ở đây - không đúng chỗ
</script>

<script>
// Middleware ở đây - SAI!
</script>
```

**Fix:**
```vue
<script setup>
// ✅ ĐÚNG - definePageMeta phải trong <script setup>
definePageMeta({
  middleware: 'auth'
})
</script>
```

---

## 6. Lỗi Pinia

### Lỗi: Store not found

```
getActivePinia() was called with no active Pinia
```

**Nguyên nhân:** Pinia chưa được khởi tạo.

**Fix:**

Trong Nuxt 4, Pinia đã được setup tự động. Kiểm tra:

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  compatibilityDate: '2025-07-15'
  // Pinia đã được auto-setup trong Nuxt 4
})
```

### Lỗi: Store không reactive khi destructure

```vue
<script setup>
import { useAuthStore } from '~/stores/auth'

const authStore = useAuthStore()

// ❌ SAI - user không reactive
const { user } = authStore

// ✅ ĐÚNG - Dùng storeToRefs
import { storeToRefs } from 'pinia'
const { user } = storeToRefs(authStore)
```

### Lỗi: State không được serialize (SSR)

```typescript
// ❌ SAI - State chứa function không serialize được
const state = ref(() => console.log('hi'))
```

**Fix:**
```typescript
// ✅ ĐÚNG - Chỉ serialize data, không phải functions
const user = ref({
  name: 'Nam',
  email: 'nam@example.com'
})
```

---

## 7. Lỗi Build

### Lỗi: Out of memory

```
FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory
```

**Fix:**
```bash
# Tăng memory limit
NODE_OPTIONS="--max-old-space-size=4096" bun run build
```

### Lỗi: Cannot find module

```
Cannot find module 'nuxt' or its corresponding type declarations
```

**Fix:**
```bash
# Xóa node_modules và cài lại
rm -rf node_modules package-lock.json
bun install
```

### Lỗi: Module not found

```
Cannot find module '@nuxtjs/something'
```

**Fix:**
```bash
# Cài module
bun add @nuxtjs/something

# Hoặc cài lại tất cả
bun install
```

---

## 🔍 Kỹ Thuật Debug

### 1. Console.log trong Nuxt

```vue
<script setup>
// Xem giá trị trong terminal (server) và browser console (client)
console.log('Debug:', someValue)

// Xem trên cả server và client
console.log('Process:', import.meta.client, import.meta.server)
</script>
```

### 2. Debug với Vue DevTools

```
1. Cài Vue DevTools extension cho trình duyệt
2. Mở DevTools → Vue tab
3. Inspect components, props, state
4. Xem Pinia stores
```

### 3. Debug với Nuxt DevTools

```
1. Bật devtools trong nuxt.config.ts
2. Nhấn Shift + D trong trình duyệt
3. Xem routes, components, state
```

### 4. Inspect Network Requests

```
1. Mở DevTools → Network tab
2. Xem các API calls
3. Kiểm tra response data
```

---

## 🎯 Quick Fix Checklist

```
KHI GẶP LỖI:
─────────────
□ Đọc lỗi trong terminal - thường có hint
□ Restart dev server (Ctrl+C → bun run dev)
□ Xóa cache (.nuxt folder)
□ Kiểm tra tên file (PascalCase?)
□ Kiểm tra import paths
□ Kiểm tra syntax (thừa/dư符, thiếu })
□ Tìm lỗi trong file nào

KHI KHÔNG TÌM ĐƯỢC:
───────────────────
1. Copy lỗi message
2. Paste vào Google: "nuxt [lỗi message]"
3. Tìm trong Nuxt Discord
4. Tạo minimal reproduction
```

---

## ▶️ Tiếp Theo

→ [02-devtools-guide.md](02-devtools-guide.md) - Hướng dẫn sử dụng DevTools

hoặc → [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Quay lại học Core Concepts

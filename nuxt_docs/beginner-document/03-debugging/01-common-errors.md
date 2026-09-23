# Lỗi Thường Gặp - Debugging Guide

> **Mục tiêu:** Biết cách debug và fix những lỗi phổ biến trong Nuxt.

## Mục lục

1. [Lỗi cài đặt](#1-lỗi-cài-đặt)
2. [Lỗi build](#2-lỗi-build)
3. [Lỗi runtime](#3-lỗi-runtime)
4. [Lỗi SSR](#4-lỗi-ssr)
5. [Lỗi TypeScript](#5-lỗi-typescript)

---

## 1. Lỗi Cài Đặt

### 1.1 "command not found: nuxi"

```bash
# ❌ Sai
nuxi init my-app

# ✅ Đúng
npx nuxi@latest init my-app

# ✅ Hoặc dùng Bun
bunx nuxi@latest init my-app
```

### 1.2 Node.js version quá cũ

```bash
# Kiểm tra version
node -v

# Lỗi: "Requires Node >= 18.0.0"

# Fix: Update Node.js
# Windows: Tải từ nodejs.org
# Mac: brew install node@20
# Linux: nvm install 20
```

### 1.3 Permission denied

```bash
# Lỗi: EACCES permission denied

# Fix: Xóa node_modules và cài lại
rm -rf node_modules package-lock.json
npm install

# Hoặc fix permission
sudo chown -R $(whoami) ~/.npm
```

### 1.4 Cache corruption

```bash
# Xóa cache và cài lại
rm -rf node_modules .nuxt .output package-lock.json
npm install
```

---

## 2. Lỗi Build

### 2.1 "Cannot find module 'nuxt'"

```bash
# Kiểm tra node_modules có nuxt không
ls node_modules | grep nuxt

# Nếu không có:
npm install

# Hoặc xóa và cài lại
rm -rf node_modules package-lock.json
npm install
```

### 2.2 "Failed to resolve import"

```typescript
// Lỗi: Cannot find module '@/components/Button'

// Kiểm tra tsconfig.json có đúng paths không
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./app/*"]
    }
  }
}
```

### 2.3 Build fails với TypeScript

```bash
# Kiểm tra TypeScript version
npx tsc --version

# Chạy type-check trước
npm run typecheck

# Hoặc bỏ qua typecheck khi build
npm run build -- --no-typescript
```

### 2.4 "Hydration mismatch"

```vue
<!-- ❌ Lỗi: Hydration mismatch -->

<!-- Server: count = 0 -->
<!-- Client: count = 1 -->

<!-- Fix: Dùng Client-only rendering -->
<ClientOnly>
  <Counter :initial-count="1" />
</ClientOnly>

<!-- Hoặc dùng onMounted để set giá trị -->
<script setup>
const count = ref(0)

onMounted(() => {
  count.value = 1 // Chỉ chạy trên client
})
</script>
```

---

## 3. Lỗi Runtime

### 3.1 "window is not defined"

```typescript
// ❌ Lỗi: SSR tries to access window

// Fix 1: Check import.meta.client
if (import.meta.client) {
  // Code chỉ chạy trên client
  window.localStorage.getItem('key')
}

// Fix 2: Dùng onMounted
onMounted(() => {
  // Code chạy sau khi mount (chỉ client)
})

// Fix 3: Client-only plugin
// plugins/example.client.ts - chỉ chạy trên client
```

### 3.2 "localStorage is not defined"

```typescript
// ❌ Lỗi
const token = localStorage.getItem('token')

// ✅ Fix: Dùng useCookie thay vì localStorage
const token = useCookie('token')

// Hoặc check client
if (import.meta.client) {
  const token = localStorage.getItem('token')
}
```

### 3.3 "useXXX cannot be called outside setup"

```typescript
// ❌ Lỗi: useRoute() được gọi outside setup

// SAI: Trong async function
async function fetchData() {
  const route = useRoute() // ❌ Lỗi!
}

// ✅ ĐÚNG: Gọi trong setup (synchronous)
const route = useRoute()

// Hoặc dùng await trong setup
const route = useRoute()
await fetchData()
```

### 3.4 "Cannot destructure property 'xxx'"

```vue
<!-- ❌ Lỗi: useRoute() return undefined -->

<!-- SAI -->
<script setup>
const { params } = useRoute() // ❌

// ✅ ĐÚNG -->
<script setup lang="ts">
const route = useRoute()
const { slug } = route.params
</script>
```

---

## 4. Lỗi SSR

### 4.1 "Hydration text content mismatch"

```vue
<!-- ❌ Lỗi: Nội dung khác nhau server/client -->

<!-- Server render: "Hello" -->
<!-- Client render: "Hello, Nam" -->

<!-- Fix: Đảm bảo data giống nhau -->
<script setup lang="ts">
// Lấy user từ cookie/server, không phải localStorage
const user = useCookie('user')
</script>
```

### 4.2 "Error during SSR"

```typescript
// Kiểm tra server logs
npm run dev

// Xem console errors trong terminal

// Wrap trong try/catch
try {
  const data = await $fetch('/api/...')
} catch (error) {
  console.error('Fetch error:', error)
}
```

### 4.3 Cookies not set on server

```typescript
// ❌ Lỗi: Cookie không persist qua SSR

// SAI
const token = ref('')
onMounted(() => {
  token.value = localStorage.getItem('token')
})

// ✅ ĐÚNG: Dùng useCookie
const token = useCookie('token', {
  maxAge: 60 * 60 * 24 // 1 day
})
```

---

## 5. Lỗi TypeScript

### 5.1 "Cannot find type definition"

```bash
# Cài type definitions
npm install -D @types/node
npm install -D vue-tsc

# Hoặc chạy
npx nuxi prepare
```

### 5.2 "Type 'xxx' is not assignable to type 'yyy'"

```typescript
// Kiểm tra kiểu
const count: number = ref(0) // ❌ ref returns Ref<number>

// ✅ ĐÚNG
const count = ref<number>(0)
const countValue: number = count.value
```

### 5.3 "Missing return type on function"

```typescript
// Thêm type cho function
const add = (a: number, b: number): number => {
  return a + b
}

// Hoặc dùng type inference
const add = (a: number, b: number) => a + b
```

---

## 🎯 Checklist Debug

```
KHI GẶP LỖI:
───────────
1. Đọc error message kỹ
2. Xem line number trong terminal
3. Copy error → Google/Stack Overflow
4. Kiểm tra Nuxt version compatibility
5. Xem docs: nuxt.com/docs
6. Hỏi Discord community

LÀM SẠCH TRƯỚC KHI DEBUG:
──────────────────────────
1. rm -rf node_modules .nuxt .output
2. npm install
3. npm run dev
```

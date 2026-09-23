# Common Gotchas - Những Bẫy Thường Gặp

> **Mục tiêu:** Biết những lỗi/phút lầm thường gặp và cách tránh.

## Mục lục

1. [SSR Gotchas](#1-ssr-gotchas)
2. [Component Gotchas](#2-component-gotchas)
3. [TypeScript Gotchas](#3-typescript-gotchas)

---

## 1. SSR Gotchas

### 1.1 Window/document undefined

```typescript
// ❌ Lỗi: window is not defined

// SAI
const localStorage = window.localStorage

// ✅ ĐÚNG
if (import.meta.client) {
  const localStorage = window.localStorage
}

// HOẶC dùng onMounted
onMounted(() => {
  // Chỉ chạy trên client
})
```

### 1.2 Hydration mismatch

```vue
<!-- ❌ Lỗi: HTML không khớp -->

<!-- Server render: <div>Hello</div> -->
<!-- Client render: <div>Hello, User</div> -->

<!-- ✅ Fix: Đảm bảo initial state nhất quán -->
<script setup lang="ts">
// Lấy từ cookie/server
const userName = useCookie('userName')
</script>
```

---

## 2. Component Gotchas

### 2.1 Props không reactive

```vue
<!-- ❌ Lỗi: Props không update component -->

<!-- SAI: Mutate props trực tiếp -->
<script setup>
const props = defineProps<{ count: number }>()
props.count = 5 // ❌ Không reactive!
</script>

<!-- ✅ ĐÚNG: Emit thay đổi -->
<script setup>
const props = defineProps<{ modelValue: number }>()
const emit = defineEmits<{ 'update:modelValue': [value: number] }>()

function increment() {
  emit('update:modelValue', props.modelValue + 1)
}
</script>
```

### 2.2 Async components

```vue
<!-- ❌ Lỗi: Component không load -->

<!-- SAI: Dynamic component -->
<component :is="HeavyComponent" />

<!-- ✅ ĐÚNG: Async component -->
<Suspense>
  <template #default>
    <AsyncComponent />
  </template>
  <template #fallback>
    <LoadingSkeleton />
  </template>
</Suspense>
```

---

## 3. TypeScript Gotchas

### 3.1 Ref type inference

```typescript
// ❌ Lỗi: Type không đúng

const count = ref(0) // count: Ref<number>

// SAI: Ép kiểu sai
const num: number = count // ❌ Lỗi!

// ✅ ĐÚNG
const num: number = count.value
```

### 3.2 DefineProps types

```typescript
// ❌ Lỗi: DefineProps không có type

// SAI
defineProps({ name: String })

// ✅ ĐÚNG
defineProps<{ name: string }>()
```

---

## ▶️ Tham Khảo

→ [Nuxt Gotchas](https://nuxt.com/docs/getting-started/configuration#nuxtconfig-tips)

# Vue 3 Watchers - Theo Dõi Thay Đổi

> **Mục tiêu:** Học cách theo dõi state thay đổi.

## Mục lục

1. [watch](#1-watch)
2. [watchEffect](#2-watcheffect)
3. [watchPostEffect](#3-watchposteffect)

---

## 1. watch

### 1.1 Cú pháp

```typescript
watch(
  source,           // Ref, reactive object, hoặc getter
  callback,         // (newValue, oldValue) => {}
  options?          // { immediate, deep, flush }
)
```

### 1.2 Ví dụ

```typescript
const count = ref(0)

// Watch single ref
watch(count, (newVal, oldVal) => {
  console.log(`Count changed: ${oldVal} → ${newVal}`)
})

// Watch getter (computed-like)
watch(
  () => count.value * 2,
  (doubled) => {
    console.log(`Doubled: ${doubled}`)
  }
)

// Watch multiple sources
const firstName = ref('')
const lastName = ref('')

watch([firstName, lastName], ([newFirst, newLast], [oldFirst, oldLast]) => {
  console.log(`${oldFirst} ${oldLast} → ${newFirst} ${newLast}`)
})
```

### 1.3 Options

```typescript
watch(count, (newVal) => {
  console.log(newVal)
}, {
  immediate: true,    // Chạy ngay lập tức
  deep: true,         // Deep watch (objects/arrays)
  flush: 'post'       // 'pre' | 'post' | 'sync'
})
```

---

## 2. watchEffect

### 2.1 Cú pháp

```typescript
watchEffect(() => {
  // Tự động track tất cả reactive dependencies
  console.log(count.value)
  console.log(user.value.name)
})
```

### 2.2 Ví dụ

```typescript
const userId = ref(1)
const userData = ref(null)

watchEffect(async () => {
  // Tự động chạy lại khi userId thay đổi
  userData.value = await fetchUser(userId.value)
})
```

---

## 3. watchPostEffect

```typescript
// Chạy sau DOM update
watchPostEffect(() => {
  // DOM đã được update
  const element = document.querySelector('.scroll-target')
  element?.scrollIntoView()
})
```

---

## ▶️ Tham Khảo

→ [Vue 3 Watchers](https://vuejs.org/guide/essentials/watchers.html)

# Computed Properties - Thuộc Tính Tính Toán

> **Mục tiêu:** Học cách dùng computed properties.

## Mục lục

1. [Computed là gì?](#1-computed-là-gì)
2. [Basic Usage](#2-basic-usage)
3. [Getters vs Setters](#3-getters-vs-setters)

---

## 1. Computed là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                         COMPUTED                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Computed = Tự động TÍNH TOÁN dựa trên dependencies              │
│                                                                     │
│  - Cached (chỉ tính lại khi dependencies thay đổi)               │
│  - Reactive (tự cập nhật khi data thay đổi)                     │
│  - Read-only (mặc định)                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Basic Usage

### 2.1 Cú pháp

```typescript
const doubled = computed(() => count.value * 2)
```

### 2.2 Ví dụ

```typescript
const firstName = ref('Nam')
const lastName = ref('Nguyen')

// Computed full name
const fullName = computed(() => `${firstName.value} ${lastName.value}`)

console.log(fullName.value) // "Nam Nguyen"

firstName.value = 'Minh'
console.log(fullName.value) // "Minh Nguyen" - tự cập nhật!
```

### 2.3 Complex Example

```typescript
const todos = ref([
  { id: 1, text: 'Learn Vue', done: true },
  { id: 2, text: 'Learn Nuxt', done: false },
  { id: 3, text: 'Build App', done: false }
])

const pendingCount = computed(() => 
  todos.value.filter(t => !t.done).length
)

const completedCount = computed(() => 
  todos.value.filter(t => t.done).length
)

const progressPercent = computed(() => {
  if (todos.value.length === 0) return 0
  return Math.round((completedCount.value / todos.value.length) * 100)
})
```

---

## 3. Getters vs Setters

### 3.1 Read-only (mặc định)

```typescript
const doubled = computed(() => count.value * 2)
doubled.value = 10 // ❌ Lỗi! Computed là read-only
```

### 3.2 With Setter

```typescript
const fullName = computed({
  get: () => `${firstName.value} ${lastName.value}`,
  set: (value) => {
    const parts = value.split(' ')
    firstName.value = parts[0]
    lastName.value = parts[1] || ''
  }
})

// Sử dụng như ref
fullName.value = 'Minh Tran'
console.log(firstName.value) // "Minh"
console.log(lastName.value)  // "Tran"
```

---

## ▶️ Tham Khảo

→ [Vue 3 Computed](https://vuejs.org/guide/essentials/computed.html)

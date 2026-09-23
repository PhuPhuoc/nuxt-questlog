# Vue 3 Reactive System - Hệ Thống Reactive

> **Mục tiêu:** Hiểu sâu về reactive system trong Vue 3.

## Mục lục

1. [Reactivity là gì?](#1-reactivity-là-gì)
2. [ref() và reactive()](#2-ref()-và-reactive())
3. [Dependency Tracking](#3-dependency-tracking)
4. [Ref Unwrapping](#4-ref-unwrapping)

---

## 1. Reactivity là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                         REACTIVITY                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Reactive = Tự động CẬP NHẬT khi data THAY ĐỔI               │
│                                                                     │
│  Ví dụ:                                                            │
│  const count = ref(0)                                             │
│  const doubled = computed(() => count.value * 2)                  │
│                                                                     │
│  count.value = 5                                                  │
│  // doubled tự động = 10                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. ref() và reactive()

### 2.1 ref()

```typescript
// ref cho primitive values
const count = ref(0)
const name = ref('Nam')
const isActive = ref(true)

// ref cho objects (cần .value)
const user = ref({ name: 'Nam', age: 25 })
user.value.age = 26
```

### 2.2 reactive()

```typescript
// reactive chỉ cho objects
const state = reactive({
  count: 0,
  name: 'Nam',
  isActive: true
})

// Không cần .value
state.count = 5
state.name = 'Minh'
```

---

## 3. Dependency Tracking

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DEPENDENCY TRACKING                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Computed = Tự động track dependencies                          │
│                                                                     │
│  const count = ref(1)                                              │
│  const doubled = computed(() => count.value * 2)                  │
│                                                                     │
│  count.value = 2                                                  │
│  // doubled re-runs vì phụ thuộc vào count                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Ref Unwrapping

```vue
<template>
  <!-- Template: KHÔNG cần .value -->
  <p>{{ count }}</p>
  <p>{{ user.name }}</p>
  
  <!-- Reactive object: tự unwrap -->
  <p>{{ state.count }}</p>
</template>
```

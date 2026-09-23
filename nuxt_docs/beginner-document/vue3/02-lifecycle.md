# Vue 3 Lifecycle - Vòng Đời Component

> **Mục tiêu:** Hiểu vòng đời của Vue component.

## Mục lục

1. [Lifecycle Diagram](#1-lifecycle-diagram)
2. [Creation Hooks](#2-creation-hooks)
3. [Mounting Hooks](#3-mounting-hooks)
4. [Updating Hooks](#4-updating-hooks)
5. [Unmounting Hooks](#5-unmounting-hooks)

---

## 1. Lifecycle Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE 3 LIFECYCLE                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Creation                                                         │
│  ├── setup()                                                     │
│  └── (onBeforeCreate, onCreated - not in <script setup>)          │
│                                                                     │
│  Mounting                                                        │
│  ├── onBeforeMount()                                             │
│  └── onMounted() ✅                                              │
│                                                                     │
│  Updating                                                        │
│  ├── onBeforeUpdate()                                            │
│  └── onUpdated() ✅                                              │
│                                                                     │
│  Unmounting                                                      │
│  ├── onBeforeUnmount()                                           │
│  └── onUnmounted() ✅                                            │
│                                                                     │
│  Error Handling                                                  │
│  └── onErrorCaptured()                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Creation Hooks

```vue
<script setup lang="ts">
// setup() runs first - KHÔNG CẦN hook cho setup

// onBeforeCreate - NOT available in <script setup>
// onCreated - NOT available in <script setup>

// Mọi thứ trong setup() = tương đương onCreated
const data = ref('initialized')
</script>
```

---

## 3. Mounting Hooks

```vue
<script setup lang="ts">
// onBeforeMount - Trước khi DOM được mount
onBeforeMount(() => {
  console.log('Sẽ mount DOM')
})

// onMounted - Sau khi DOM đã mount
onMounted(() => {
  console.log('Đã mount DOM')
  
  // DOM có sẵn, ví dụ: event listeners
  const element = document.querySelector('.my-element')
})
</script>
```

---

## 4. Updating Hooks

```vue
<script setup lang="ts">
const count = ref(0)

// onBeforeUpdate - Trước khi re-render
onBeforeUpdate(() => {
  console.log('Sẽ re-render')
})

// onUpdated - Sau khi re-render
onUpdated(() => {
  console.log('Đã re-render')
})

function increment() {
  count.value++
}
</script>
```

---

## 5. Unmounting Hooks

```vue
<script setup lang="ts">
// onBeforeUnmount - Trước khi unmount
onBeforeUnmount(() => {
  console.log('Sẽ unmount')
})

// onUnmounted - Sau khi unmount
onUnmounted(() => {
  console.log('Đã unmount')
  
  // Cleanup: remove event listeners, timers
  clearInterval(timerId)
  window.removeEventListener('resize', handleResize)
})
</script>
```

---

## ▶️ Tham Khảo

→ [Vue 3 Lifecycle](https://vuejs.org/api/options-lifecycle.html)

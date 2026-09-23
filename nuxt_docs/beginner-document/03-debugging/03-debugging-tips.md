# Mẹo Debug - Kỹ Thuật Debug Hiệu Quả

> **Mục tiêu:** Học các kỹ thuật debug nâng cao trong Nuxt.

## Mục lục

1. [Console Debugging](#1-console-debugging)
2. [Vue DevTools](#2-vue-devtools)
3. [Network Debugging](#3-network-debugging)
4. [Performance Debugging](#4-performance-debugging)

---

## 1. Console Debugging

### 1.1 Logging State

```vue
<script setup lang="ts">
const count = ref(0)

// Log khi state thay đổi
watch(count, (newVal, oldVal) => {
  console.log('Count changed:', { oldVal, newVal })
}, { immediate: true })
</script>
```

### 1.2 Logging Composables

```typescript
// composables/useLogger.ts
export function useLogger() {
  return {
    log: (message: string, data?: any) => {
      console.log(`[LOG] ${message}`, data)
    },
    error: (message: string, error?: any) => {
      console.error(`[ERROR] ${message}`, error)
    },
    warn: (message: string, data?: any) => {
      console.warn(`[WARN] ${message}`, data)
    }
  }
}
```

---

## 2. Vue DevTools

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE DEVTOOLS                                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Vue DevTools = Chrome/Firefox extension                            │
│                                                                     │
│  Cài: Chrome Web Store → Vue DevTools                              │
│                                                                     │
│  Tính năng:                                                       │
│  ├── Component inspector                                           │
│  ├── State viewer                                                  │
│  ├── Event timeline                                               │
│  └── Performance profiler                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Network Debugging

### 3.1 Xem API Requests

1. Mở DevTools (F12)
2. Tab Network
3. Filter: Fetch/XHR
4. Xem request/response

### 3.2 Log Requests

```typescript
// plugins/api-logger.ts
export default defineNuxtPlugin((nuxtApp) => {
  // Log all $fetch requests
  nuxtApp.hook('app:created', () => {
    console.log('Nuxt app created')
  })
})
```

---

## 4. Performance Debugging

### 4.1 Vue Performance DevTools

```vue
<script setup lang="ts">
import { defineComponent } from 'vue'

// Wrap component để measure performance
const SlowComponent = defineAsyncComponent(() => 
  import('./SlowComponent.vue')
)
</script>
```

### 4.2 Console Timer

```typescript
console.time('fetchPosts')

const posts = await $fetch('/api/posts')

console.timeEnd('fetchPosts') // Log: fetchPosts: 123ms
```

---

## ▶️ Tóm Tắt

```
TOOLS:
──────
1. console.log - Debug cơ bản
2. Vue DevTools - Component inspection
3. Nuxt DevTools - Route/state debugging
4. Network Tab - API debugging
5. Performance Tab - Speed analysis
```

# Pinia Advanced Techniques - Kỹ Thuật Nâng Cao

> **Mục tiêu:** Học các kỹ thuật nâng cao với Pinia.

## Mục lục

1. [Store Plugins](#1-store-plugins)
2. [Hot Module Replacement](#2-hot-module-replacement)
3. [Testing Stores](#3-testing-stores)

---

## 1. Store Plugins

### 1.1 Plugin Structure

```typescript
// plugins/my-plugin.ts
export const myPlugin = definePiniaPlugin(({ store }) => {
  // Chạy khi store được tạo
  console.log(`Store "${store.$id}" created`)
  
  // Subscribe to changes
  store.$subscribe((mutation, state) => {
    console.log('State changed:', mutation)
  })
  
  // Subscribe to actions
  store.$onAction(({ name, args, after, onError }) => {
    console.log(`Action "${name}" called with`, args)
    
    after((result) => {
      console.log(`Action "${name}" completed with`, result)
    })
    
    onError((error) => {
      console.error(`Action "${name}" failed:`, error)
    })
  })
})
```

### 1.2 Register Plugin

```typescript
// plugins/pinia.ts
import { myPlugin } from './my-plugin'

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.$pinia.use(myPlugin)
})
```

---

## 2. Hot Module Replacement

### 2.1 Auto HMR

Pinia tự động hỗ trợ HMR trong dev mode.

---

## 3. Testing Stores

### 3.1 Unit Testing

```typescript
// stores/counter.test.ts
import { setActivePinia, createPinia } from 'pinia'
import { useCounterStore } from './counter'

describe('Counter Store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })
  
  test('increment', () => {
    const store = useCounterStore()
    store.increment()
    expect(store.count).toBe(1)
  })
})
```

---

## ▶️ Tham Khảo

→ [Pinia Cookbook](https://pinia.vuejs.org/cookbook/index.html)

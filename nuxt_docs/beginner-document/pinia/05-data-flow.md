# Pinia Data Flow - Luồng Dữ Liệu

> **Mục tiêu:** Hiểu cách data flow trong Pinia.

## Mục lục

1. [Data Flow Pattern](#1-data-flow-pattern)
2. [Components ↔ Store](#2-components--store)
3. [Store ↔ API](#3-store--api)

---

## 1. Data Flow Pattern

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PINIA DATA FLOW                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Component                                                         │
│     │                                                             │
│     ├── Reads state ──────────→ Store (Getters)                   │
│     │                                                             │
│     └── Calls action ─────────→ Store (Actions)                    │
│                                    │                                │
│                              Modifies state                         │
│                                    │                                │
│                                    ↓                                │
│                               Computed                             │
│                                    │                                │
│                                    ↓                                │
│                              Component re-renders                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Components ↔ Store

### 2.1 Reading State

```vue
<script setup lang="ts">
const cartStore = useCartStore()

// Cách 1: Direct access
const items = computed(() => cartStore.items)

// Cách 2: storeToRefs (destructuring)
import { storeToRefs } from 'pinia'
const { items, total } = storeToRefs(cartStore)

// Actions - gọi trực tiếp
const { addItem } = cartStore
</script>
```

---

## 3. Store ↔ API

### 3.1 Actions as API Layer

```typescript
export const useProductStore = defineStore('products', () => {
  const products = ref<Product[]>([])
  
  // Actions gọi API
  async function fetchProducts() {
    products.value = await $fetch('/api/products')
  }
  
  async function createProduct(data: CreateProductData) {
    const newProduct = await $fetch('/api/products', {
      method: 'POST',
      body: data
    })
    products.value.push(newProduct)
    return newProduct
  }
  
  return { products, fetchProducts, createProduct }
})
```

---

## ▶️ Tham Khảo

→ [Pinia Data Flow](https://pinia.vuejs.org/core-concepts/)

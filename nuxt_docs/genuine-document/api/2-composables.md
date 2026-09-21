# Composables

Danh sách các Composables có sẵn trong Nuxt.

## useAppConfig

### Mô Tả

Truy cập app config reactive được định nghĩa trong project.

### Cách Sử Dụng

```ts
const appConfig = useAppConfig()

console.log(appConfig)
```

## useRoute

### Mô Tả

Composable `useRoute` trả về route hiện tại.

`useRoute` là một wrapper xung quanh composable cùng tên từ vue-router, cung cấp quyền truy cập vào route hiện tại trong ứng dụng Nuxt.

### Ví Dụ

```vue
<script setup lang="ts">
const route = useRoute()
const { data: mountain } = await useFetch(`/api/mountains/${route.params.slug}`)
</script>

<template>
  <div>
    <h1>{{ mountain.title }}</h1>
    <p>{{ mountain.description }}</p>
  </div>
</template>
```

### API

- **fullPath**: URL được encode chứa path, query và hash
- **hash**: Phần hash đã decode của URL
- **query**: Truy cập route query parameters
- **matched**: Mảng các matched routes
- **meta**: Custom data đính kèm vào record
- **name**: Tên duy nhất cho route record
- **path**: Phần pathname đã encode của URL
- **redirectedFrom**: Route location trước khi đến route hiện tại

### Các Vấn Đề Thường Gặp

#### Vấn Đề Đồng Bộ Route

Điều quan trọng là sử dụng `useRoute()` từ Nuxt thay vì từ vue-router để tránh các vấn đề đồng bộ.

```ts
// Không sử dụng useRoute từ vue-router
import { useRoute } from 'vue-router'

// Sử dụng Nuxt's useRoute composable
import { useRoute } from '#app'
```

## useRuntimeConfig

### Mô Tả

Truy cập các biến runtime config với composable `useRuntimeConfig`.

### Cách Sử Dụng

```vue
<script setup lang="ts">
const config = useRuntimeConfig()
</script>
```

```ts
export default defineEventHandler((event) => {
  const config = useRuntimeConfig(event)
})
```

### Định Nghĩa Runtime Config

```ts
export default defineNuxtConfig({
  runtimeConfig: {
    // Private keys chỉ khả dụng trên server
    apiSecret: '123',

    // Public keys được expose cho client
    public: {
      apiBase: process.env.NUXT_PUBLIC_API_BASE || '/api',
    },
  },
})
```

### Namespace `app`

Nuxt sử dụng namespace `app` với các keys bao gồm `baseURL` và `cdnURL`.

#### `app.baseURL`

Mặc định `baseURL` được đặt là `'/'`.

```ts
const baseURL = config.app.baseURL
```

#### `app.cdnURL`

```ts
const cdnURL = config.app.cdnURL
```

## useFetch

### Mô Tả

Fetch dữ liệu từ một API endpoint với một composable SSR-friendly.

`useFetch` là một wrapper tiện lợi xung quanh `useAsyncData` và `$fetch`. Nó tự động tạo key cho request, cung cấp type hints cho URL dựa trên server routes, và suy luận API response type.

### Cách Sử Dụng

```vue
<script setup lang="ts">
const { data, status, error, refresh, clear } = await useFetch('/api/modules', {
  pick: ['title'],
})
</script>
```

### Reactive Keys và Shared State

```vue
<script setup lang="ts">
const route = useRoute()
const id = computed(() => route.params.id)

const { data: post } = await useFetch(() => `/api/posts/${id.value}`)
</script>
```

### Tùy Chọn

- **key**: Unique key cho de-duplication
- **method**: HTTP request method (mặc định: `'GET'`)
- **query**: Query/search params
- **body**: Request body
- **headers**: Request headers
- **baseURL**: Base URL cho request
- **server**: Fetch trên server (mặc định: `true`)
- **lazy**: Resolve sau khi route loads (mặc định: `false`)
- **immediate**: Bắt đầu request ngay (mặc định: `true`)
- **default**: Factory cho giá trị mặc định
- **transform**: Function để transform kết quả
- **pick**: Chỉ chọn các keys được chỉ định
- **watch**: Array các reactive sources để watch và auto-refresh

### Return Values

- **data**: Kết quả của async fetch
- **refresh**: Function để refresh data thủ công
- **execute**: Alias cho `refresh`
- **error**: Error object nếu fetch thất bại
- **status**: Status của data request (`'idle' | 'pending' | 'success' | 'error'`)
- **pending**: `true` trong khi request đang thực thi
- **clear**: Reset data về undefined

## useState

### Mô Tả

Composable `useState` tạo một reactive và SSR-friendly shared state.

### Cách Sử Dụng

```ts
const count = useState('counter', () => Math.round(Math.random() * 100))
```

### Sử Dụng `shallowRef`

```ts
const state = useState('my-shallow-state', () => shallowRef({ deep: 'not reactive' }))
```

### Type

```ts
export function useState<T> (init?: () => T | Ref<T>): Ref<T>
export function useState<T> (key: string, init?: () => T | Ref<T>): Ref<T>
```

### Lưu Ý Quan Trọng

Bởi vì dữ liệu bên trong `useState` sẽ được serialize thành JSON, điều quan trọng là nó không chứa bất kỳ thứ gì không thể serialize, như classes, functions hoặc symbols.

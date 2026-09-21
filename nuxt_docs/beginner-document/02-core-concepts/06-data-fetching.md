# Data Fetching & SSR

> **Mục tiêu:** Hiểu các cách fetch data trong Nuxt và cách SSR hoạt động.

## Mục lục

1. [SSR là gì?](#1-ssr-là-gì)
2. [useFetch](#2-usefetch)
3. [useAsyncData](#3-useasyncdatas)
4. [$fetch](#4-fetch)
5. [Khi nào dùng cái nào?](#5-khi-nào-dùng-cái-nào)
6. [SSR Considerations](#6-ssr-considerations)

---

## 1. SSR là gì?

### Khái niệm

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SSR LÀ GÌ?                                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SSR = Server-Side Rendering                                        │
│                                                                     │
│  SPA (Single Page Application):                                     │
│  ├── Browser yêu cầu HTML trống                               │
│  ├── Download JS                                                    │
│  ├── JS render content                                             │
│  └── → Chậm, SEO kém                                            │
│                                                                     │
│  SSR (Server-Side Rendering):                                      │
│  ├── Server render HTML với data                               │
│  ├── Browser nhận HTML đầy đủ                                 │
│  ├── Hiển thị NGAY (không cần đợi JS)                      │
│  └── → Nhanh, SEO tốt                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Nuxt SSR Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT SSR FLOW                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. User request /blog/vue3                                       │
│                    ↓                                                 │
│  2. Server: Gọi API /api/posts/vue3                             │
│                    ↓                                                 │
│  3. Server: Render page với data                                │
│                    ↓                                                 │
│  4. Server: Trả HTML cho browser                                 │
│                    ↓                                                 │
│  5. Browser: Hiển thị HTML NGAY LẬP TỨC                      │
│                    ↓                                                 │
│  6. Browser: Hydration (gắn Vue vào HTML)                      │
│                    ↓                                                 │
│  7. App hoạt động như SPA                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. useFetch

### 2.1 Cú pháp cơ bản

```vue
<script setup>
// basic usage
const { data, pending, error, refresh } = await useFetch('/api/posts')

// với options
const { data, pending, error, refresh } = await useFetch('/api/posts', {
  method: 'GET',
  query: { limit: 10 },
  headers: { Authorization: 'Bearer token' }
})
</script>

<template>
  <!-- loading state -->
  <div v-if="pending">Loading...</div>

  <!-- error state -->
  <div v-else-if="error">Error: {{ error.message }}</div>

  <!-- data -->
  <div v-else-if="data">
    <h1>{{ data.title }}</h1>
    <p>{{ data.content }}</p>
  </div>
</template>
```

### 2.2 Với Query Parameters

```vue
<script setup>
const route = useRoute()

// Tự động reactive - fetch lại khi query thay đổi
const { data } = await useFetch('/api/search', {
  query: {
    q: route.query.q,
    page: route.query.page || 1
  }
})
</script>
```

### 2.3 Với Transform

```vue
<script setup>
const { data } = await useFetch('/api/users', {
  transform: (users) => {
    return users.map(user => ({
      ...user,
      fullName: `${user.firstName} ${user.lastName}`,
      createdAt: new Date(user.createdAt).toLocaleDateString()
    }))
  }
})
</script>
```

### 2.4 Lazy Fetching

```vue
<script setup>
// Lazy: Chỉ fetch khi component mount
// Dùng cho user-interactive data (không cần SEO)
const { data, pending } = await useLazyFetch('/api/comments')

// Hoặc
const { data, pending } = useFetch('/api/comments', {
  lazy: true
})
</script>
```

---

## 3. useAsyncData

### 3.1 Khi nào dùng?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    useAsyncData vs useFetch                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  useFetch:                                                        │
│  ├── Đơn giản, nhanh                                             │
│  ├── Tự động serialize params                                    │
│  ├── Tốt cho simple GET requests                                │
│                                                                     │
│  useAsyncData:                                                    │
│  ├── Linh hoạt hơn                                               │
│  ├── Gọi nhiều APIs trong 1 lần                               │
│  ├── Custom logic trước/sau fetch                              │
│  ├── Tốt cho complex data transformations                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Cú pháp cơ bản

```vue
<script setup>
const { data, pending, error, refresh } = await useAsyncData(
  'unique-key', // Cache key
  () => $fetch('/api/posts')
)
</script>
```

### 3.3 Gọi nhiều APIs

```vue
<script setup>
const { data: userData, pending: userPending } = await useAsyncData(
  'user',
  () => $fetch('/api/user')
)

const { data: postsData, pending: postsPending } = await useAsyncData(
  'posts',
  () => $fetch('/api/posts')
)

// Hoặc gọi song song
const [{ data: user }, { data: posts }] = await Promise.all([
  useAsyncData('user', () => $fetch('/api/user')),
  useAsyncData('posts', () => $fetch('/api/posts'))
])
</script>
```

### 3.4 Với Complex Logic

```vue
<script setup>
const { data: post } = await useAsyncData(
  'post',
  async () => {
    // 1. Fetch post
    const post = await $fetch(`/api/posts/${route.params.slug}`)

    // 2. Fetch related posts
    const related = await $fetch(`/api/posts/${post.id}/related`)

    // 3. Fetch comments
    const comments = await $fetch(`/api/posts/${post.id}/comments`)

    // 4. Transform data
    return {
      ...post,
      related,
      comments,
      formattedDate: formatDate(post.createdAt)
    }
  }
)
</script>
```

---

## 4. $fetch

### 4.1 Khi nào dùng?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    $FETCH DÙNG KHI NÀO?                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Dùng $fetch khi:                                                 │
│  ├── KHÔNG PHẢI trong setup() hoặc lifecycle hooks              │
│  ├── Trong event handlers (@click, @submit)                      │
│  ├── Trong composables (không phải setup)                        │
│  ├── Trong actions (Pinia store actions)                        │
│  └── Trong server routes                                          │
│                                                                     │
│  KHÔNG DÙNG useFetch trong những trường hợp trên!            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 Trong Event Handlers

```vue
<script setup>
async function handleSubmit() {
  // Gửi form
  const result = await $fetch('/api/contact', {
    method: 'POST',
    body: {
      name: form.name,
      email: form.email,
      message: form.message
    }
  })

  if (result.success) {
    alert('Gửi thành công!')
  }
}

async function deletePost(id) {
  await $fetch(`/api/posts/${id}`, {
    method: 'DELETE'
  })
  // Refresh data
  refresh()
}
</script>
```

### 4.3 Trong Composable

```vue
<script setup>
// composables/usePost.ts
export const usePost = (slug) => {
  const post = ref(null)

  async function fetchPost() {
    post.value = await $fetch(`/api/posts/${slug}`)
  }

  async function updatePost(data) {
    const updated = await $fetch(`/api/posts/${slug}`, {
      method: 'PUT',
      body: data
    })
    post.value = updated
  }

  return {
    post,
    fetchPost,
    updatePost
  }
}
</script>
```

### 4.4 Trong Pinia Actions

```typescript
// stores/posts.ts
export const usePostStore = defineStore('posts', () => {
  const posts = ref([])

  async function fetchAll() {
    posts.value = await $fetch('/api/posts')
  }

  async function create(data) {
    const newPost = await $fetch('/api/posts', {
      method: 'POST',
      body: data
    })
    posts.value.unshift(newPost)
  }

  async function remove(id) {
    await $fetch(`/api/posts/${id}`, { method: 'DELETE' })
    posts.value = posts.value.filter(p => p.id !== id)
  }

  return { posts, fetchAll, create, remove }
})
```

---

## 5. Khi Nào Dùng Cái Nào?

### Decision Tree

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATA FETCHING DECISION TREE                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Bạn đang fetch data cho PAGE?                                    │
│         ↓                                                           │
│    CÓ → Dùng useFetch() hoặc useAsyncData()                     │
│         ↓                                                           │
│    Cần gọi nhiều APIs hoặc có logic phức tạp?                   │
│         ↓                                                           │
│    CÓ → useAsyncData()                                          │
│    KHÔNG → useFetch()                                           │
│                                                                     │
│  ──────────────────────────────────────────────────────────────   │
│                                                                     │
│  Bạn đang fetch data TRONG:                                       │
│         ↓                                                           │
│    Event handler / Actions / Composable KHÔNG phải setup?         │
│         ↓                                                           │
│    CÓ → Dùng $fetch()                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Examples

```vue
<!-- 1. Simple page data - useFetch -->
<script setup>
const { data: posts } = await useFetch('/api/posts')
</script>

<!-- 2. Page data với params - useFetch -->
<script setup>
const route = useRoute()
const { data: post } = await useFetch(`/api/posts/${route.params.slug}`)
</script>

<!-- 3. Complex page data - useAsyncData -->
<script setup>
const { data } = await useAsyncData('dashboard', async () => {
  const [user, stats, notifications] = await Promise.all([
    $fetch('/api/user'),
    $fetch('/api/stats'),
    $fetch('/api/notifications')
  ])
  return { user, stats, notifications }
})
</script>

<!-- 4. Form submit - $fetch -->
<script setup>
async function onSubmit() {
  await $fetch('/api/posts', {
    method: 'POST',
    body: formData
  })
}
</script>

<!-- 5. On-demand refresh - $fetch trong action -->
<script setup>
const postStore = usePostStore()

async function refresh() {
  await postStore.fetchAll()
}
</script>
```

---

## 6. SSR Considerations

### 6.1 Client-Only Code

```vue
<script setup>
// Chạy trên cả server và client
const data = ref('Hello')

// Chỉ chạy trên client
onMounted(() => {
  // localStorage, window, etc. - chỉ an toàn trong onMounted
  const token = localStorage.getItem('token')
  console.log(token)
})

// Cách khác: dùng import.meta.client
if (import.meta.client) {
  // Code này chỉ chạy trên client
  console.log(window.innerWidth)
}
</script>
```

### 6.2 useCookie thay vì localStorage

```vue
<script setup>
// ❌ localStorage không hoạt động trên server
// Sẽ gây lỗi khi server render
const token = localStorage.getItem('token')

// ✅ useCookie hoạt động trên cả server và client
const token = useCookie('token')

// Đọc
console.log(token.value)

// Ghi
token.value = 'new-token'

// Xóa
token.value = null
</script>
```

### 6.3 Avoiding Window/Document Access on Server

```vue
<script setup>
// ❌ SAI - Gây lỗi khi SSR
const width = window.innerWidth
const isOnline = navigator.onLine

// ✅ ĐÚNG - Kiểm tra môi trường trước
const width = ref(0)
const isOnline = ref(true)

onMounted(() => {
  width.value = window.innerWidth
  isOnline.value = navigator.onLine
})

// Hoặc dùng computed với check
const deviceWidth = computed(() => {
  if (import.meta.server) return 1024 // Default cho SSR
  return window.innerWidth
})
</script>
```

### 6.4 Lazy Loading cho Heavy Data

```vue
<script setup>
// Non-lazy: Đợi data trước khi render page
// User thấy loading state, tốt cho SEO
const { data } = await useFetch('/api/heavy-data')

// Lazy: Render page trước, fetch data song song
// User thấy page ngay, data load sau (với loading indicator)
const { data, pending } = await useLazyFetch('/api/heavy-data')
</script>

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATA FETCHING CHEAT SHEET                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  PAGE DATA (trong setup):                                         │
│  ├── useFetch('/api/...') → Simple GET requests                  │
│  └── useAsyncData('key', () => ...) → Complex logic              │
│                                                                     │
│  ON-DEMAND (trong handlers):                                       │
│  └── $fetch('/api/...') → Event handlers, actions                 │
│                                                                     │
│  SSR SAFE:                                                         │
│  ├── useCookie() → Thay localStorage                             │
│  ├── onMounted() → Browser-only code                              │
│  └── import.meta.client → Check environment                        │
│                                                                     │
│  STATE:                                                            │
│  ├── data → Fetched data                                        │
│  ├── pending → Loading state                                      │
│  ├── error → Error state                                        │
│  └── refresh() → Refetch data                                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [07-state-management.md](07-state-management.md) - State Management (ref, useState, Pinia)

hoặc → [04-routing-advanced.md](04-routing-advanced.md) - Quay lại Routing

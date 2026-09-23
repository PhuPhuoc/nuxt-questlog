# Data Fetching - Lấy Dữ Liệu Từ API

> **Mục tiêu:** Học cách fetch data trong Nuxt với useFetch, useAsyncData, và $fetch.

## Mục lục

1. [Tổng quan Data Fetching](#1-tổng-quan-data-fetching)
2. [useFetch](#2-usefetch)
3. [useAsyncData](#3-useasyncdata)
4. [$fetch](#4-fetch)
5. [Server Routes](#5-server-routes)
6. [Error Handling](#6-error-handling)

---

## 1. Tổng Quan Data Fetching

### 1.1 Các cách fetch data trong Nuxt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATA FETCHING METHODS                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  useFetch()                                                       │
│  ├── Wrapper around useAsyncData + $fetch                        │
│  ├── Auto-caching, reactive params                                 │
│  └── Tốt nhất cho: Page-level data fetching                      │
│                                                                     │
│  useAsyncData()                                                   │
│  ├── Fetch data với custom key                                    │
│  ├── Kiểm soát caching                                            │
│  └── Tốt nhất cho: Complex data transformations                   │
│                                                                     │
│  $fetch()                                                         │
│  ├── Raw HTTP client                                               │
│  ├── Không có caching tự động                                     │
│  └── Tốt nhất cho: Mutations, one-off requests                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Khi nào dùng gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHI NÀO DÙNG GÌ?                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  useFetch()                                                        │
│  ├── GET requests đơn giản                                        │
│  ├── Params thay đổi theo reactive values                         │
│  └── Page component data                                           │
│                                                                     │
│  useAsyncData()                                                   │
│  ├── Khi cần custom key/caching                                   │
│  ├── Complex data transformations                                  │
│  └── Khi cần nhiều requests cùng lúc                            │
│                                                                     │
│  $fetch()                                                         │
│  ├── Form submissions                                              │
│  ├── Mutations (POST, PUT, DELETE)                                │
│  ├── Event handlers (click, submit)                                │
│  └── One-off requests trong composables                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. useFetch

### 2.1 Cú pháp cơ bản

```vue
<script setup lang="ts">
// Cú pháp cơ bản
const { data, pending, error, refresh } = await useFetch('/api/posts')

// data: Ref chứa response
// pending: Boolean - đang loading
// error: Error object nếu có lỗi
// refresh: Function - gọi lại request
</script>

<template>
  <div>
    <div v-if="pending">Loading...</div>
    <div v-else-if="error">Error: {{ error.message }}</div>
    <div v-else>
      <article v-for="post in data" :key="post.id">
        {{ post.title }}
      </article>
    </div>
  </div>
</template>
```

### 2.2 Với TypeScript

```vue
<script setup lang="ts">
// Định nghĩa kiểu
interface Post {
  id: number
  title: string
  content: string
  author: string
  publishedAt: string
}

const { data: posts, pending, error } = await useFetch<Post[]>('/api/posts', {
  default: () => [] as Post[]
})
</script>

<template>
  <div>
    <article v-for="post in posts" :key="post.id">
      <h2>{{ post.title }}</h2>
      <p>{{ post.author }} - {{ post.publishedAt }}</p>
    </article>
  </div>
</template>
```

### 2.3 Với Query Parameters

```vue
<script setup lang="ts">
// Reactive query params
const page = ref(1)
const category = ref('vue')

// Tự động re-fetch khi params thay đổi
const { data: posts } = await useFetch('/api/posts', {
  query: {
    page: page,        // → ?page=1
    category: category  // → &category=vue
  }
})
</script>

<template>
  <div>
    <select v-model="category">
      <option value="vue">Vue</option>
      <option value="react">React</option>
    </select>
    
    <button @click="page++">Next Page</button>
    
    <div v-for="post in data" :key="post.id">
      {{ post.title }}
    </div>
  </div>
</template>
```

### 2.4 Với Path Parameters

```vue
<script setup lang="ts">
// Dynamic route params
const route = useRoute()
const slug = route.params.slug

// Fetch theo slug
const { data: post } = await useFetch(`/api/posts/${slug}`)
</script>
```

### 2.5 Options đầy đủ

```vue
<script setup lang="ts">
const { data, pending, error, refresh } = await useFetch('/api/posts', {
  // HTTP Method (mặc định: GET)
  method: 'GET',
  
  // Query parameters
  query: { page: 1, limit: 10 },
  
  // Headers
  headers: {
    Authorization: 'Bearer token'
  },
  
  // Request body
  body: { title: 'New Post' },
  
  // Transform response
  transform: (response) => {
    return response.posts.map(post => ({
      ...post,
      titleUppercase: post.title.toUpperCase()
    }))
  },
  
  // Watch dependencies
  watch: [page, category],
  
  // Caching
  getCachedData(key, nuxtApp) {
    return nuxtApp.payload.data[key]
  },
  
  // Lazy (không blocking navigation)
  lazy: true,
  
  // Default value
  default: () => []
})
</script>
```

---

## 3. useAsyncData

### 3.1 Cú pháp cơ bản

```vue
<script setup lang="ts">
// useAsyncData cần key duy nhất
const { data, pending, error, refresh } = await useAsyncData(
  'posts',  // Unique key for caching
  () => $fetch('/api/posts')
)
</script>
```

### 3.2 Với Complex Logic

```vue
<script setup lang="ts">
const { data: dashboardData, pending, error } = await useAsyncData(
  'dashboard',
  async () => {
    // Gọi nhiều APIs
    const [user, posts, stats] = await Promise.all([
      $fetch('/api/user'),
      $fetch('/api/posts'),
      $fetch('/api/stats')
    ])
    
    return {
      user,
      posts,
      stats,
      totalPosts: posts.length
    }
  }
)
</script>
```

### 3.3 Refresh

```vue
<script setup lang="ts">
const { data, refresh, clear } = await useFetch('/api/posts')

// Refresh dữ liệu
async function reloadPosts() {
  await refresh()
}

// Clear cache và data
function clearData() {
  clear()
}
</script>

<template>
  <button @click="reloadPosts">Refresh</button>
  <button @click="clearData">Clear</button>
</template>
```

### 3.4 Computed Query

```vue
<script setup lang="ts">
// Reactive filter
const filter = ref('')

// useAsyncData với computed query
const { data: filteredPosts } = useAsyncData(
  () => `posts-${filter.value}`,  // Dynamic key
  () => $fetch('/api/posts', {
    query: { filter: filter.value }
  })
)

// Watch thủ công nếu cần
watch(filter, async () => {
  await refreshNuxtData(`posts-${filter.value}`)
})
</script>
```

---

## 4. $fetch

### 4.1 Cú pháp cơ bản

```vue
<script setup lang="ts">
// Simple GET request
const user = await $fetch('/api/user/1')

// POST request
const newPost = await $fetch('/api/posts', {
  method: 'POST',
  body: {
    title: 'New Post',
    content: 'Content here'
  }
})

// Với headers
const secureData = await $fetch('/api/secure', {
  headers: {
    Authorization: `Bearer ${token.value}`
  }
})
</script>
```

### 4.2 Trong Event Handlers

```vue
<script setup lang="ts">
async function handleSubmit() {
  try {
    // $fetch tốt cho mutations trong handlers
    await $fetch('/api/posts', {
      method: 'POST',
      body: formData
    })
    
    // Refresh list
    refresh()
  } catch (error) {
    console.error('Failed to create post:', error)
  }
}
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <!-- Form fields -->
    <button type="submit">Submit</button>
  </form>
</template>
```

### 4.3 Trong Composables

```typescript
// composables/usePost.ts
export const usePost = (slug: string) => {
  const { data, pending, error, refresh } = useAsyncData(
    `post-${slug}`,
    () => $fetch(`/api/posts/${slug}`)
  )
  
  const createComment = async (comment: string) => {
    return await $fetch(`/api/posts/${slug}/comments`, {
      method: 'POST',
      body: { content: comment }
    })
  }
  
  return {
    post: data,
    pending,
    error,
    refresh,
    createComment
  }
}
```

---

## 5. Server Routes

### 5.1 Tạo API Endpoint

```typescript
// server/api/posts/index.get.ts
export default defineEventHandler(async (event) => {
  // Query params
  const query = getQuery(event)
  const page = Number(query.page) || 1
  const limit = Number(query.limit) || 10
  
  // Fetch từ database (giả lập)
  const posts = await db.posts.findMany({
    skip: (page - 1) * limit,
    take: limit,
    orderBy: { createdAt: 'desc' }
  })
  
  return {
    posts,
    page,
    totalPages: Math.ceil(posts.length / limit)
  }
})
```

### 5.2 POST Endpoint

```typescript
// server/api/posts/index.post.ts
export default defineEventHandler(async (event) => {
  // Read body
  const body = await readBody(event)
  
  // Validate
  if (!body.title) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Title is required'
    })
  }
  
  // Create post
  const newPost = await db.posts.create({
    data: {
      title: body.title,
      content: body.content
    }
  })
  
  return newPost
})
```

### 5.3 Dynamic Routes

```
📁 server/api/posts/
├── 📄 index.get.ts       → GET /api/posts
├── 📄 index.post.ts      → POST /api/posts
├── 📄 [id].get.ts       → GET /api/posts/:id
├── 📄 [id].put.ts       → PUT /api/posts/:id
└── 📄 [id].delete.ts    → DELETE /api/posts/:id
```

```typescript
// server/api/posts/[id].get.ts
export default defineEventHandler(async (event) => {
  const id = Number(getRouterParam(event, 'id'))
  
  const post = await db.posts.findUnique({
    where: { id }
  })
  
  if (!post) {
    throw createError({
      statusCode: 404,
      statusMessage: 'Post not found'
    })
  }
  
  return post
})
```

### 5.4 Sử dụng trong Component

```vue
<script setup lang="ts">
// GET posts
const { data: posts } = await useFetch('/api/posts', {
  query: { page: 1, limit: 10 }
})

// Create post
async function createPost() {
  const newPost = await $fetch('/api/posts', {
    method: 'POST',
    body: { title: 'New Post', content: '...' }
  })
  
  // Refresh list
  await refresh()
}
</script>
```

---

## 6. Error Handling

### 6.1 Xử lý lỗi trong useFetch

```vue
<script setup lang="ts">
const { data, pending, error } = await useFetch('/api/posts', {
  // Retry on error
  retry: 3,
  
  // Transform errors
  onRequestError({ error }) {
    console.error('Request error:', error)
  },
  
  onResponseError({ response }) {
    console.error('Response error:', response.status)
  }
})
</script>

<template>
  <div>
    <div v-if="pending">Loading...</div>
    
    <div v-else-if="error">
      <h2>Error occurred!</h2>
      <p>{{ error.message }}</p>
      <button @click="refresh()">Retry</button>
    </div>
    
    <div v-else>
      {{ data }}
    </div>
  </div>
</template>
```

### 6.2 Global Error Handler

```vue
<!-- app.vue -->
<script setup lang="ts">
const error = useError()

function handleError() {
  // Clear error
  clearError()
  
  // Redirect
  navigateTo('/')
}
</script>

<template>
  <div>
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
    
    <!-- Error overlay -->
    <div v-if="error" class="error-overlay">
      <h1>{{ error.statusCode }}</h1>
      <p>{{ error.message }}</p>
      <button @click="handleError">Go Home</button>
    </div>
  </div>
</template>
```

### 6.3 try/catch với $fetch

```vue
<script setup lang="ts">
async function submitForm() {
  try {
    isLoading.value = true
    
    await $fetch('/api/posts', {
      method: 'POST',
      body: formData
    })
    
    // Success
    successMessage.value = 'Post created!'
    refresh()
  } catch (err: any) {
    // Handle error
    errorMessage.value = err.data?.message || 'Failed to create post'
  } finally {
    isLoading.value = false
  }
}
</script>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DATA FETCHING CHEAT SHEET                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  useFetch(url, options):                                           │
│  ├── Auto-caching với key                                         │
│  ├── Reactive query params                                         │
│  └── Dùng cho: GET requests, page data                           │
│                                                                     │
│  useAsyncData(key, fn, options):                                  │
│  ├── Custom key/caching control                                    │
│  └── Dùng cho: Complex logic, multiple requests                   │
│                                                                     │
│  $fetch(url, options):                                            │
│  ├── No auto-caching                                              │
│  └── Dùng cho: Mutations, event handlers                          │
│                                                                     │
│  OPTIONS:                                                          │
│  ├── query - Query parameters                                     │
│  ├── headers - Custom headers                                     │
│  ├── body - Request body                                          │
│  ├── method - HTTP method                                         │
│  ├── transform - Transform response                                │
│  ├── lazy - Don't block navigation                                 │
│  └── default - Default value                                       │
│                                                                     │
│  RESPONSE:                                                         │
│  ├── data - Response data                                         │
│  ├── pending - Loading state                                       │
│  ├── error - Error object                                         │
│  └── refresh() - Re-fetch data                                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [07-state-management.md](08-state-management.md) - State Management

hoặc → [03-debugging/01-common-errors.md](../03-debugging/01-common-errors.md) - Lỗi thường gặp

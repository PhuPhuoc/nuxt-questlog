# Server Routes - API Endpoints

> **Mục tiêu:** Học cách tạo API endpoints trong Nuxt với server/ directory.

## Mục lục

1. [Giới thiệu Server Routes](#1-giới-thiệu-server-routes)
2. [Tạo GET Endpoint](#2-tạo-get-endpoint)
3. [Tạo POST Endpoint](#3-tạo-post-endpoint)
4. [Dynamic Routes](#4-dynamic-routes)
5. [Error Handling](#5-error-handling)

---

## 1. Giới Thiệu Server Routes

### 1.1 Server Routes là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SERVER ROUTES                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Server Routes = API Endpoints trong Nuxt                          │
│                                                                     │
│  Đặt trong: server/api/                                           │
│  Tự động tạo routes RESTful                                      │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── GET /api/users → server/api/users.get.ts                     │
│  ├── POST /api/users → server/api/users.post.ts                   │
│  └── DELETE /api/users/:id → server/api/users/[id].delete.ts     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Cấu trúc thư mục

```
📁 server/
├── 📁 api/
│   ├── 📄 users.get.ts         → GET /api/users
│   ├── 📄 users.post.ts        → POST /api/users
│   ├── 📄 users/[id].get.ts   → GET /api/users/:id
│   ├── 📄 users/[id].put.ts   → PUT /api/users/:id
│   └── 📄 users/[id].delete.ts → DELETE /api/users/:id
├── 📁 middleware/
└── 📁 utils/
```

---

## 2. Tạo GET Endpoint

### 2.1 GET đơn giản

```typescript
// server/api/users.get.ts
export default defineEventHandler(async (event) => {
  // Trả về danh sách users
  return [
    { id: 1, name: 'Nam', email: 'nam@example.com' },
    { id: 2, name: 'Minh', email: 'minh@example.com' }
  ]
})
```

### 2.2 GET với Query Parameters

```typescript
// server/api/posts.get.ts
export default defineEventHandler(async (event) => {
  // Lấy query params
  const query = getQuery(event)
  
  const page = Number(query.page) || 1
  const limit = Number(query.limit) || 10
  const category = query.category as string | undefined
  
  // Mock data
  let posts = [
    { id: 1, title: 'Post 1', category: 'vue' },
    { id: 2, title: 'Post 2', category: 'react' },
    { id: 3, title: 'Post 3', category: 'vue' }
  ]
  
  // Filter
  if (category) {
    posts = posts.filter(p => p.category === category)
  }
  
  // Paginate
  const start = (page - 1) * limit
  const paginatedPosts = posts.slice(start, start + limit)
  
  return {
    posts: paginatedPosts,
    page,
    total: posts.length,
    totalPages: Math.ceil(posts.length / limit)
  }
})
```

### 2.3 Sử dụng GET trong Component

```vue
<script setup lang="ts">
const { data: users } = await useFetch('/api/users')

// Hoặc với params
const { data: posts } = await useFetch('/api/posts', {
  query: { page: 1, category: 'vue' }
})
</script>
```

---

## 3. Tạo POST Endpoint

### 3.1 POST đơn giản

```typescript
// server/api/users.post.ts
export default defineEventHandler(async (event) => {
  // Đọc body
  const body = await readBody(event)
  
  // Validate
  if (!body.name || !body.email) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Name and email are required'
    })
  }
  
  // Tạo user (mock)
  const newUser = {
    id: Date.now(),
    name: body.name,
    email: body.email,
    createdAt: new Date().toISOString()
  }
  
  return newUser
})
```

### 3.2 POST với Validation

```typescript
// server/api/posts.post.ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  
  // Validate
  const errors: string[] = []
  
  if (!body.title) {
    errors.push('Title is required')
  }
  
  if (body.title && body.title.length < 3) {
    errors.push('Title must be at least 3 characters')
  }
  
  if (!body.content) {
    errors.push('Content is required')
  }
  
  if (errors.length > 0) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Validation failed',
      data: { errors }
    })
  }
  
  // Create post
  const newPost = {
    id: Date.now(),
    title: body.title,
    content: body.content,
    published: false,
    createdAt: new Date().toISOString()
  }
  
  return newPost
})
```

---

## 4. Dynamic Routes

### 4.1 GET với ID

```typescript
// server/api/users/[id].get.ts
export default defineEventHandler(async (event) => {
  // Lấy params
  const id = Number(getRouterParam(event, 'id'))
  
  if (isNaN(id)) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Invalid ID'
    })
  }
  
  // Mock user
  const user = {
    id,
    name: 'User ' + id,
    email: `user${id}@example.com`
  }
  
  return user
})
```

### 4.2 PUT (Update)

```typescript
// server/api/users/[id].put.ts
export default defineEventHandler(async (event) => {
  const id = Number(getRouterParam(event, 'id'))
  const body = await readBody(event)
  
  // Update user (mock)
  const updatedUser = {
    id,
    name: body.name || 'Updated User',
    email: body.email || `user${id}@example.com`
  }
  
  return updatedUser
})
```

### 4.3 DELETE

```typescript
// server/api/users/[id].delete.ts
export default defineEventHandler(async (event) => {
  const id = Number(getRouterParam(event, 'id'))
  
  // Delete user (mock)
  
  return {
    success: true,
    message: `User ${id} deleted`
  }
})
```

### 4.4 Nested Dynamic Routes

```
📁 server/api/
└── 📁 users/
    └── 📁 [userId]/
        ├── 📄 posts.get.ts       → GET /api/users/:userId/posts
        └── 📄 [postId].get.ts  → GET /api/users/:userId/posts/:postId
```

```typescript
// server/api/users/[userId]/posts.get.ts
export default defineEventHandler(async (event) => {
  const userId = getRouterParam(event, 'userId')
  
  return [
    { id: 1, userId, title: 'Post 1' },
    { id: 2, userId, title: 'Post 2' }
  ]
})
```

---

## 5. Error Handling

### 5.1 Tạo Error

```typescript
export default defineEventHandler(async (event) => {
  try {
    // Code
  } catch (error) {
    throw createError({
      statusCode: 500,
      statusMessage: 'Internal Server Error',
      data: { message: 'Something went wrong' }
    })
  }
})
```

### 5.2 HTTP Status Codes

```typescript
// 200: Success
// 201: Created
// 400: Bad Request
// 401: Unauthorized
// 403: Forbidden
// 404: Not Found
// 500: Internal Server Error
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SERVER ROUTES CHEAT SHEET                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  FILE → ROUTE:                                                     │
│  ├── users.get.ts → GET /api/users                                │
│  ├── users.post.ts → POST /api/users                               │
│  ├── [id].get.ts → GET /api/:id                                    │
│                                                                     │
│  HANDLERS:                                                         │
│  ├── getQuery(event) → Query params                               │
│  ├── getRouterParam(event, 'id') → URL params                    │
│  ├── readBody(event) → Request body                              │
│  └── createError({ statusCode, statusMessage }) → Error          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [02-database-integration.md](02-database-integration.md) - Kết nối Database

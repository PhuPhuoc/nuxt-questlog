# Authentication - Xác Thực Người Dùng

> **Mục tiêu:** Học cách implement authentication trong Nuxt.

## Mục lục

1. [Authentication Flow](#1-authentication-flow)
2. [Auth Store](#2-auth-store)
3. [Login/Register API](#3-loginregister-api)
4. [Middleware Protection](#4-middleware-protection)

---

## 1. Authentication Flow

### 1.1 Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTHENTICATION FLOW                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. User nhập credentials                                          │
│  2. Gửi POST /api/auth/login                                      │
│  3. Server verify → Trả về token                                 │
│  4. Client lưu token vào cookie                                   │
│  5. Redirect đến dashboard                                        │
│  6. Middleware kiểm tra auth                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Auth Store

### 2.1 Auth Store với Pinia

```typescript
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref<User | null>(null)
  const token = useCookie('auth_token', {
    maxAge: 60 * 60 * 24 * 7, // 7 days
    secure: true,
    httpOnly: true
  })
  
  // Getters
  const isAuthenticated = computed(() => !!token.value)
  const isAdmin = computed(() => user.value?.role === 'admin')
  
  // Actions
  async function login(credentials: { email: string; password: string }) {
    try {
      const response = await $fetch<{ user: User; token: string }>('/api/auth/login', {
        method: 'POST',
        body: credentials
      })
      
      token.value = response.token
      user.value = response.user
      
      return response
    } catch (error) {
      console.error('Login failed:', error)
      throw error
    }
  }
  
  function logout() {
    user.value = null
    token.value = null
    navigateTo('/login')
  }
  
  async function fetchUser() {
    if (!token.value) return
    
    try {
      user.value = await $fetch<User>('/api/auth/me', {
        headers: {
          Authorization: `Bearer ${token.value}`
        }
      })
    } catch {
      logout()
    }
  }
  
  return {
    user: readonly(user),
    token,
    isAuthenticated,
    isAdmin,
    login,
    logout,
    fetchUser
  }
})
```

---

## 3. Login/Register API

### 3.1 Login API

```typescript
// server/api/auth/login.post.ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  
  // Validate
  if (!body.email || !body.password) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Email and password are required'
    })
  }
  
  // Verify user (mock - thực tế dùng database)
  const user = await verifyCredentials(body.email, body.password)
  
  if (!user) {
    throw createError({
      statusCode: 401,
      statusMessage: 'Invalid credentials'
    })
  }
  
  // Generate token
  const token = generateToken(user)
  
  return {
    user,
    token
  }
})
```

### 3.2 Get Current User API

```typescript
// server/api/auth/me.get.ts
export default defineEventHandler(async (event) => {
  const authHeader = getHeader(event, 'authorization')
  
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    throw createError({
      statusCode: 401,
      statusMessage: 'Unauthorized'
    })
  }
  
  const token = authHeader.slice(7)
  const user = await verifyToken(token)
  
  if (!user) {
    throw createError({
      statusCode: 401,
      statusMessage: 'Invalid token'
    })
  }
  
  return user
})
```

---

## 4. Middleware Protection

### 4.1 Auth Middleware

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const authStore = useAuthStore()
  
  // Public routes
  const publicRoutes = ['/login', '/register', '/forgot-password']
  
  if (!authStore.isAuthenticated && !publicRoutes.includes(to.path)) {
    return navigateTo({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  }
  
  // Redirect if already logged in
  if (authStore.isAuthenticated && publicRoutes.includes(to.path)) {
    return navigateTo('/dashboard')
  }
})
```

---

## ▶️ Tham Khảo

→ [Auth in Nuxt](https://nuxt.com/docs/examples/auth)

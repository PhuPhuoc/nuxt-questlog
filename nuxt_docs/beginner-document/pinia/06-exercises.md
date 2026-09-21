# 6. Bài tập Thực hành

## Bài tập 1: useAuthStore - Quản lý Authentication

### Yêu cầu

Tạo một Auth Store hoàn chỉnh với các tính năng:
- Đăng nhập / Đăng xuất
- Lưu token vào Cookie (SSR-safe)
- Fetch thông tin user hiện tại
- Protected routes check

### Đáp án

```typescript
// stores/auth.ts
import { defineStore } from 'pinia'
import type { User, LoginCredentials } from '~/types'

interface AuthState {
  user: User | null
  isLoading: boolean
  error: string | null
}

export const useAuthStore = defineStore('auth', () => {
  // ===== STATE =====
  const user = ref<User | null>(null)
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  // ===== COOKIE (SSR-Safe) =====
  const token = useCookie<string | null>('auth_token', {
    default: () => null,
    maxAge: 60 * 60 * 24 * 7, // 1 week
    watch: true,
    secure: true,
    sameSite: 'lax'
  })

  // ===== GETTERS =====
  const isAuthenticated = computed(() => !!token.value && !!user.value)
  const userName = computed(() => user.value?.name ?? 'Guest')
  const userEmail = computed(() => user.value?.email ?? '')
  const userAvatar = computed(() => user.value?.avatar ?? null)
  const isAdmin = computed(() => user.value?.role === 'admin')

  // ===== ACTIONS =====

  /**
   * Đăng nhập với credentials
   */
  async function login(credentials: LoginCredentials): Promise<boolean> {
    isLoading.value = true
    error.value = null

    try {
      const response = await $fetch<{ user: User; token: string }>('/api/auth/login', {
        method: 'POST',
        body: credentials
      })

      // Cập nhật state
      user.value = response.user
      token.value = response.token

      return true
    } catch (e) {
      error.value = e instanceof Error
        ? e.message
        : 'Đăng nhập thất bại. Vui lòng thử lại.'

      // Log error cho debugging (production nên dùng logging service)
      console.error('[Auth] Login failed:', e)

      return false
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Đăng xuất - clear all auth state
   */
  function logout() {
    user.value = null
    token.value = null
    error.value = null
  }

  /**
   * Fetch thông tin user hiện tại từ server
   */
  async function fetchCurrentUser(): Promise<void> {
    // Không fetch nếu đã có user hoặc không có token
    if (user.value || !token.value) return

    isLoading.value = true
    error.value = null

    try {
      const currentUser = await $fetch<User>('/api/auth/me', {
        headers: {
          Authorization: `Bearer ${token.value}`
        }
      })

      user.value = currentUser
    } catch (e) {
      // Token có thể đã hết hạn
      if (e instanceof Error && e.message.includes('401')) {
        logout()
      }
      error.value = 'Không thể lấy thông tin người dùng'
      console.error('[Auth] Fetch user failed:', e)
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Đăng ký tài khoản mới
   */
  async function register(data: {
    name: string
    email: string
    password: string
  }): Promise<boolean> {
    isLoading.value = true
    error.value = null

    try {
      const response = await $fetch<{ user: User; token: string }>('/api/auth/register', {
        method: 'POST',
        body: data
      })

      user.value = response.user
      token.value = response.token

      return true
    } catch (e) {
      error.value = e instanceof Error
        ? e.message
        : 'Đăng ký thất bại. Vui lòng thử lại.'
      console.error('[Auth] Register failed:', e)
      return false
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Cập nhật thông tin user
   */
  async function updateProfile(updates: Partial<User>): Promise<boolean> {
    if (!user.value) return false

    isLoading.value = true
    error.value = null

    try {
      const updated = await $fetch<User>(`/api/users/${user.value.id}`, {
        method: 'PATCH',
        body: updates,
        headers: {
          Authorization: `Bearer ${token.value}`
        }
      })

      user.value = updated
      return true
    } catch (e) {
      error.value = 'Cập nhật thất bại'
      console.error('[Auth] Update profile failed:', e)
      return false
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Reset error state
   */
  function clearError() {
    error.value = null
  }

  // ===== COMPUTED: Helpers =====

  /**
   * Check nếu user có quyền cụ thể
   */
  function hasPermission(permission: string): boolean {
    return user.value?.permissions?.includes(permission) ?? false
  }

  /**
   * Check nếu user có role cụ thể
   */
  function hasRole(role: string): boolean {
    return user.value?.role === role
  }

  return {
    // State (readonly để enforce mutations qua actions)
    user: readonly(user),
    isLoading: readonly(isLoading),
    error: readonly(error),
    token, // Cookie có thể đọc trực tiếp

    // Getters
    isAuthenticated,
    userName,
    userEmail,
    userAvatar,
    isAdmin,

    // Actions
    login,
    logout,
    fetchCurrentUser,
    register,
    updateProfile,
    clearError,

    // Helpers
    hasPermission,
    hasRole
  }
})
```

### Sử dụng trong Component

```vue
<!-- components/AuthForm.vue -->
<script setup lang="ts">
const authStore = useAuthStore()
const router = useRouter()

const form = reactive({
  email: '',
  password: ''
})

async function handleLogin() {
  const success = await authStore.login(form)

  if (success) {
    router.push('/dashboard')
  }
}
</script>

<template>
  <form @submit.prevent="handleLogin">
    <div v-if="authStore.error" class="error-message">
      {{ authStore.error }}
    </div>

    <input
      v-model="form.email"
      type="email"
      placeholder="Email"
      required
    />

    <input
      v-model="form.password"
      type="password"
      placeholder="Password"
      required
    />

    <button type="submit" :disabled="authStore.isLoading">
      {{ authStore.isLoading ? 'Đang đăng nhập...' : 'Đăng nhập' }}
    </button>
  </form>
</template>
```

```vue
<!-- components/UserAvatar.vue -->
<script setup lang="ts">
const authStore = useAuthStore()
</script>

<template>
  <div v-if="authStore.isAuthenticated" class="user-info">
    <img
      v-if="authStore.userAvatar"
      :src="authStore.userAvatar"
      :alt="authStore.userName"
      class="avatar"
    />
    <div v-else class="avatar-placeholder">
      {{ authStore.userName.charAt(0).toUpperCase() }}
    </div>
    <span>{{ authStore.userName }}</span>
    <button @click="authStore.logout">Đăng xuất</button>
  </div>
</template>
```

### Middleware cho Protected Routes

```typescript
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to) => {
  const authStore = useAuthStore()

  // Nếu chưa đăng nhập, redirect về login
  if (!authStore.isAuthenticated) {
    return navigateTo('/login', {
      query: { redirect: to.fullPath }
    })
  }
})
```

```typescript
// middleware/admin.ts
export default defineNuxtRouteMiddleware(() => {
  const authStore = useAuthStore()

  if (!authStore.isAdmin) {
    return navigateTo('/')
  }
})
```

---

## Bài tập 2: useCartStore - Giỏ hàng hoàn chỉnh

### Yêu cầu

Tạo Cart Store với các tính năng:
- Thêm / Sửa / Xóa sản phẩm
- Tính tổng tiền (subtotal, tax, total)
- Áp dụng coupon/discount
- Sync với server
- Persist local state

### Đáp án

```typescript
// stores/cart.ts
import { defineStore } from 'pinia'
import type { Product, CartItem, Coupon } from '~/types'

interface CartState {
  items: CartItem[]
  couponCode: string | null
  appliedCoupon: Coupon | null
  isLoading: boolean
  error: string | null
}

export const useCartStore = defineStore('cart', () => {
  // ===== STATE =====
  const items = ref<CartItem[]>([])
  const couponCode = ref<string | null>(null)
  const appliedCoupon = ref<Coupon | null>(null)
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  // ===== GETTERS =====

  /**
   * Số lượng sản phẩm trong giỏ
   */
  const itemCount = computed(() =>
    items.value.reduce((sum, item) => sum + item.quantity, 0)
  )

  /**
   * Số lượng loại sản phẩm khác nhau
   */
  const uniqueItemCount = computed(() => items.value.length)

  /**
   * Giỏ có trống không
   */
  const isEmpty = computed(() => items.value.length === 0)

  /**
   * Tính tổng tiền chưa có discount
   */
  const subtotal = computed(() =>
    items.value.reduce((sum, item) => {
      const price = item.discountedPrice ?? item.price
      return sum + price * item.quantity
    }, 0)
  )

  /**
   * Tính discount amount
   */
  const discountAmount = computed(() => {
    if (!appliedCoupon.value) return 0

    if (appliedCoupon.value.type === 'percentage') {
      return subtotal.value * (appliedCoupon.value.value / 100)
    }

    // Fixed amount
    return Math.min(appliedCoupon.value.value, subtotal.value)
  })

  /**
   * Tổng sau discount
   */
  const afterDiscount = computed(() =>
    Math.max(0, subtotal.value - discountAmount.value)
  )

  /**
   * Thuế (10%)
   */
  const taxRate = 0.1
  const taxAmount = computed(() => afterDiscount.value * taxRate)

  /**
   * Tổng cộng cuối cùng
   */
  const total = computed(() => afterDiscount.value + taxAmount.value)

  /**
   * Format tiền VND
   */
  const formattedSubtotal = computed(() => formatCurrency(subtotal.value))
  const formattedDiscount = computed(() => formatCurrency(discountAmount.value))
  const formattedTax = computed(() => formatCurrency(taxAmount.value))
  const formattedTotal = computed(() => formatCurrency(total.value))

  /**
   * Check sản phẩm có trong giỏ không
   */
  function isInCart(productId: string | number): boolean {
    return items.value.some(item => item.productId === productId)
  }

  /**
   * Lấy item từ giỏ hàng
   */
  function getItem(productId: string | number): CartItem | undefined {
    return items.value.find(item => item.productId === productId)
  }

  // ===== HELPER FUNCTIONS =====

  function formatCurrency(amount: number): string {
    return new Intl.NumberFormat('vi-VN', {
      style: 'currency',
      currency: 'VND'
    }).format(amount)
  }

  // ===== ACTIONS =====

  /**
   * Thêm sản phẩm vào giỏ
   */
  function addItem(product: Product, quantity: number = 1) {
    const existingItem = items.value.find(
      item => item.productId === product.id
    )

    if (existingItem) {
      // Tăng số lượng nếu đã có
      existingItem.quantity += quantity
    } else {
      // Thêm mới
      const newItem: CartItem = {
        id: `cart_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`,
        productId: product.id,
        name: product.name,
        price: product.price,
        discountedPrice: product.discountedPrice,
        image: product.images?.[0] ?? null,
        quantity,
        metadata: product.metadata
      }
      items.value.push(newItem)
    }
  }

  /**
   * Thêm với productId (đã có product data)
   */
  function addItemById(
    productId: string | number,
    name: string,
    price: number,
    quantity: number = 1,
    image?: string
  ) {
    const existingItem = items.value.find(
      item => item.productId === productId
    )

    if (existingItem) {
      existingItem.quantity += quantity
    } else {
      items.value.push({
        id: `cart_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`,
        productId,
        name,
        price,
        image: image ?? null,
        quantity
      })
    }
  }

  /**
   * Cập nhật số lượng sản phẩm
   */
  function updateQuantity(productId: string | number, quantity: number) {
    const item = items.value.find(i => i.productId === productId)

    if (!item) return

    if (quantity <= 0) {
      removeItem(productId)
    } else {
      item.quantity = quantity
    }
  }

  /**
   * Tăng số lượng
   */
  function incrementQuantity(productId: string | number) {
    const item = items.value.find(i => i.productId === productId)
    if (item) {
      item.quantity++
    }
  }

  /**
   * Giảm số lượng
   */
  function decrementQuantity(productId: string | number) {
    const item = items.value.find(i => i.productId === productId)
    if (item) {
      if (item.quantity <= 1) {
        removeItem(productId)
      } else {
        item.quantity--
      }
    }
  }

  /**
   * Xóa sản phẩm khỏi giỏ
   */
  function removeItem(productId: string | number) {
    items.value = items.value.filter(item => item.productId !== productId)
  }

  /**
   * Xóa tất cả sản phẩm
   */
  function clearCart() {
    items.value = []
    appliedCoupon.value = null
    couponCode.value = null
  }

  /**
   * Áp dụng coupon/discount
   */
  async function applyCoupon(code: string): Promise<boolean> {
    if (!code.trim()) {
      error.value = 'Vui lòng nhập mã coupon'
      return false
    }

    isLoading.value = true
    error.value = null

    try {
      const coupon = await $fetch<Coupon>(`/api/coupons/${code}`)

      // Validate coupon
      const now = new Date()
      if (coupon.expiresAt && new Date(coupon.expiresAt) < now) {
        error.value = 'Coupon đã hết hạn'
        return false
      }

      if (coupon.minOrderAmount && subtotal.value < coupon.minOrderAmount) {
        error.value = `Đơn hàng tối thiểu ${formatCurrency(coupon.minOrderAmount)} để áp dụng coupon này`
        return false
      }

      appliedCoupon.value = coupon
      couponCode.value = code

      return true
    } catch (e) {
      error.value = 'Mã coupon không hợp lệ'
      return false
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Xóa coupon đã áp dụng
   */
  function removeCoupon() {
    appliedCoupon.value = null
    couponCode.value = null
  }

  /**
   * Sync cart với server (khi user đăng nhập)
   */
  async function syncWithServer() {
    const authStore = useAuthStore()

    if (!authStore.isAuthenticated) return

    isLoading.value = true
    error.value = null

    try {
      // Merge server cart với local cart
      const serverCart = await $fetch<{ items: CartItem[] }>('/api/cart', {
        headers: {
          Authorization: `Bearer ${authStore.token}`
        }
      })

      // Simple merge strategy: server wins for duplicates
      for (const serverItem of serverCart.items) {
        const localItem = items.value.find(
          i => i.productId === serverItem.productId
        )
        if (localItem) {
          // Keep the one with more quantity
          if (serverItem.quantity > localItem.quantity) {
            localItem.quantity = serverItem.quantity
          }
        } else {
          items.value.push(serverItem)
        }
      }

      // Save merged cart back to server
      await $fetch('/api/cart', {
        method: 'PUT',
        body: { items: items.value },
        headers: {
          Authorization: `Bearer ${authStore.token}`
        }
      })
    } catch (e) {
      error.value = 'Không thể đồng bộ giỏ hàng'
      console.error('[Cart] Sync failed:', e)
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Checkout - gửi order lên server
   */
  async function checkout(): Promise<{ success: boolean; orderId?: string }> {
    if (isEmpty.value) {
      error.value = 'Giỏ hàng trống'
      return { success: false }
    }

    isLoading.value = true
    error.value = null

    try {
      const authStore = useAuthStore()

      const order = await $fetch<{ orderId: string }>('/api/orders', {
        method: 'POST',
        body: {
          items: items.value,
          couponCode: couponCode.value,
          subtotal: subtotal.value,
          discount: discountAmount.value,
          tax: taxAmount.value,
          total: total.value
        },
        headers: authStore.isAuthenticated
          ? { Authorization: `Bearer ${authStore.token}` }
          : {}
      })

      // Clear cart after successful order
      clearCart()

      return { success: true, orderId: order.orderId }
    } catch (e) {
      error.value = 'Đặt hàng thất bại. Vui lòng thử lại.'
      console.error('[Cart] Checkout failed:', e)
      return { success: false }
    } finally {
      isLoading.value = false
    }
  }

  /**
   * Load cart từ localStorage (client-only)
   */
  function loadFromStorage() {
    if (import.meta.server) return

    try {
      const stored = localStorage.getItem('cart_items')
      if (stored) {
        const data = JSON.parse(stored)
        items.value = data.items ?? []
        couponCode.value = data.couponCode ?? null
      }
    } catch (e) {
      console.error('[Cart] Failed to load from storage:', e)
    }
  }

  /**
   * Save cart vào localStorage (client-only)
   */
  function saveToStorage() {
    if (import.meta.server) return

    try {
      localStorage.setItem('cart_items', JSON.stringify({
        items: items.value,
        couponCode: couponCode.value
      }))
    } catch (e) {
      console.error('[Cart] Failed to save to storage:', e)
    }
  }

  // Auto-save khi cart thay đổi
  watch(
    () => items.value,
    () => saveToStorage(),
    { deep: true }
  )

  return {
    // State
    items,
    couponCode,
    appliedCoupon,
    isLoading,
    error,

    // Getters
    itemCount,
    uniqueItemCount,
    isEmpty,
    subtotal,
    discountAmount,
    afterDiscount,
    taxAmount,
    total,
    formattedSubtotal,
    formattedDiscount,
    formattedTax,
    formattedTotal,

    // Helpers
    isInCart,
    getItem,

    // Actions
    addItem,
    addItemById,
    updateQuantity,
    incrementQuantity,
    decrementQuantity,
    removeItem,
    clearCart,
    applyCoupon,
    removeCoupon,
    syncWithServer,
    checkout,
    loadFromStorage,
    saveToStorage
  }
})
```

### Sử dụng trong Component

```vue
<!-- components/CartDrawer.vue -->
<script setup lang="ts">
const cartStore = useCartStore()

// Load từ localStorage khi mount (client-only)
onMounted(() => {
  cartStore.loadFromStorage()
})

const isCartOpen = ref(false)

function toggleCart() {
  isCartOpen.value = !isCartOpen.value
}

async function handleCheckout() {
  const result = await cartStore.checkout()

  if (result.success) {
    isCartOpen.value = false
    navigateTo(`/order-confirmation/${result.orderId}`)
  }
}
</script>

<template>
  <div>
    <!-- Cart Toggle Button -->
    <button @click="toggleCart" class="cart-button">
      <span>🛒</span>
      <span v-if="cartStore.itemCount > 0" class="badge">
        {{ cartStore.itemCount }}
      </span>
    </button>

    <!-- Cart Drawer -->
    <div v-if="isCartOpen" class="cart-drawer">
      <div class="cart-header">
        <h2>Giỏ hàng ({{ cartStore.itemCount }})</h2>
        <button @click="toggleCart">×</button>
      </div>

      <!-- Empty State -->
      <div v-if="cartStore.isEmpty" class="empty-state">
        <p>Giỏ hàng trống</p>
        <button @click="navigateTo('/products')">
          Tiếp tục mua sắm
        </button>
      </div>

      <!-- Cart Items -->
      <div v-else class="cart-items">
        <div
          v-for="item in cartStore.items"
          :key="item.id"
          class="cart-item"
        >
          <img v-if="item.image" :src="item.image" :alt="item.name" />
          <div class="item-info">
            <h4>{{ item.name }}</h4>
            <p>{{ item.discountedPrice ?? item.price | currency }}</p>
          </div>
          <div class="quantity-controls">
            <button @click="cartStore.decrementQuantity(item.productId)">
              -
            </button>
            <span>{{ item.quantity }}</span>
            <button @click="cartStore.incrementQuantity(item.productId)">
              +
            </button>
          </div>
          <button
            @click="cartStore.removeItem(item.productId)"
            class="remove-btn"
          >
            ×
          </button>
        </div>
      </div>

      <!-- Cart Summary -->
      <div v-if="!cartStore.isEmpty" class="cart-summary">
        <div class="summary-row">
          <span>Tạm tính:</span>
          <span>{{ cartStore.formattedSubtotal }}</span>
        </div>
        <div v-if="cartStore.discountAmount > 0" class="summary-row discount">
          <span>Giảm giá:</span>
          <span>-{{ cartStore.formattedDiscount }}</span>
        </div>
        <div class="summary-row tax">
          <span>Thuế (10%):</span>
          <span>{{ cartStore.formattedTax }}</span>
        </div>
        <div class="summary-row total">
          <span>Tổng cộng:</span>
          <span>{{ cartStore.formattedTotal }}</span>
        </div>

        <button
          @click="handleCheckout"
          :disabled="cartStore.isLoading"
          class="checkout-btn"
        >
          {{ cartStore.isLoading ? 'Đang xử lý...' : 'Thanh toán' }}
        </button>
      </div>

      <!-- Error -->
      <div v-if="cartStore.error" class="error-message">
        {{ cartStore.error }}
      </div>
    </div>
  </div>
</template>
```

```vue
<!-- components/ProductCard.vue -->
<script setup lang="ts">
import type { Product } from '~/types'

const props = defineProps<{
  product: Product
}>()

const cartStore = useCartStore()

function handleAddToCart() {
  cartStore.addItem(props.product)
}
</script>

<template>
  <div class="product-card">
    <img :src="product.images?.[0]" :alt="product.name" />
    <h3>{{ product.name }}</h3>
    <p v-if="product.discountedPrice" class="original-price">
      {{ product.price | currency }}
    </p>
    <p class="price">
      {{ (product.discountedPrice ?? product.price) | currency }}
    </p>
    <button
      @click="handleAddToCart"
      :disabled="cartStore.isInCart(product.id)"
    >
      {{ cartStore.isInCart(product.id) ? 'Đã thêm' : 'Thêm vào giỏ' }}
    </button>
  </div>
</template>
```

### Plugin để Persist Cart

```typescript
// plugins/cart-persist.client.ts
export default defineNuxtPlugin(() => {
  const cartStore = useCartStore()

  // Load từ localStorage khi app khởi động
  cartStore.loadFromStorage()

  // Watch và save mỗi khi cart thay đổi
  watch(
    () => cartStore.items,
    () => cartStore.saveToStorage(),
    { deep: true }
  )
})
```

---

## Bonus: Type Definitions

```typescript
// types/user.ts
export interface User {
  id: string | number
  name: string
  email: string
  avatar?: string
  role: 'user' | 'admin'
  permissions?: string[]
  createdAt: string
}

export interface LoginCredentials {
  email: string
  password: string
}

// types/product.ts
export interface Product {
  id: string | number
  name: string
  description?: string
  price: number
  discountedPrice?: number
  images?: string[]
  category?: string
  metadata?: Record<string, any>
}

export interface CartItem {
  id: string
  productId: string | number
  name: string
  price: number
  discountedPrice?: number
  image?: string | null
  quantity: number
  metadata?: Record<string, any>
}

export interface Coupon {
  id: string
  code: string
  type: 'percentage' | 'fixed'
  value: number
  minOrderAmount?: number
  expiresAt?: string
  usageLimit?: number
  usedCount?: number
}
```

---

## Tóm tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                      EXERCISES SUMMARY                               │
│                                                                      │
│  ✅ useAuthStore                                                     │
│     ├── Login/Register/Logout                                       │
│     ├── Token in Cookie (SSR-safe)                                 │
│     ├── Fetch current user                                          │
│     ├── Permissions & Roles                                         │
│     └── Middleware cho protected routes                             │
│                                                                      │
│  ✅ useCartStore                                                     │
│     ├── CRUD operations cho cart items                              │
│     ├── Subtotal, Tax, Discount calculations                        │
│     ├── Coupon system                                              │
│     ├── LocalStorage persistence                                    │
│     └── Server sync khi authenticated                              │
│                                                                      │
│  💡 Key Patterns                                                    │
│     ├── Dùng useCookie() thay vì localStorage cho sensitive data   │
│     ├── readonly() cho state trong return                         │
│     ├── Try/catch trong async actions                              │
│     └── watch() để auto-persist                                   │
└─────────────────────────────────────────────────────────────────────┘
```

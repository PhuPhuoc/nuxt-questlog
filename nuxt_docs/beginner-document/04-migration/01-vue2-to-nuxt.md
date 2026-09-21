# Migration: Vue 2 → Nuxt 3/4

> **Mục tiêu:** Hướng dẫn người dùng Vue 2 chuyển sang Nuxt 3/4.

## Mục lục

1. [Tại sao nên chuyển?](#1-tại-sao-nên-chuyển)
2. [Thay đổi lớn](#2-thay-đổi-lớn)
3. [Options API → Composition API](#3-options-api--composition-api)
4. [Vuex → Pinia](#4-vuex--pinia)
5. [Vue Router → Nuxt Routing](#5-vue-router--nuxt-routing)
6. [Component Changes](#6-component-changes)
7. [Common Gotchas](#7-common-gotchas)

---

## 1. Tại Sao Nên Chuyển?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT 3/4 vs VUE 2                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Server-Side Rendering (SSR) sẵn có                        │
│  ✅ Auto-imports - không cần import thủ công                     │
│  ✅ File-based routing - không cần cấu hình router               │
│  ✅ Composition API - code tổ chức tốt hơn                       │
│  ✅ Pinia thay Vuex - API đơn giản hơn                          │
│  ✅ TypeScript first - hỗ trợ tốt hơn                           │
│  ✅ Performance tốt hơn                                          │
│  ✅ Hỗ trợ tương lai tốt hơn                                   │
│                                                                     │
│  ⚠️ Vue 2 sẽ hết hỗ trợ vào cuối 2024                         │
│  ⚠️ Nhiều thư viện Vue 2 không còn update nữa                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Thay Đổi Lớn

### So sánh Vue 2 vs Nuxt 3/4

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE 2          →          NUXT 3/4                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Cấu trúc:                                                        │
│  ├── src/                         → app/                          │
│  ├── store/                        → app/stores/                  │
│  ├── router/index.js              → app/pages/                    │
│  └── components/                   → app/components/               │
│                                                                     │
│  API:                                                              │
│  ├── Vuex                         → Pinia                          │
│  ├── Vue Router                   → File-based routing             │
│  ├── Options API                  → Composition API (recommended)  │
│  └── this.$...                    → Composables                    │
│                                                                     │
│  Lifecycle:                                                        │
│  ├── created()                    → Trong setup()                 │
│  ├── mounted()                    → onMounted()                    │
│  ├── beforeDestroy()              → onBeforeUnmount()              │
│  └── destroyed()                  → onUnmounted()                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Options API → Composition API

### 3.1 Data

```vue
<!-- Vue 2 - Options API -->
<script>
export default {
  data() {
    return {
      count: 0,
      user: null
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 - Composition API -->
<script setup>
const count = ref(0)
const user = ref(null)

// Không cần return - tự động expose
</script>
```

### 3.2 Methods

```vue
<!-- Vue 2 -->
<script>
export default {
  data() {
    return { count: 0 }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
const count = ref(0)

function increment() {
  count.value++
}
</script>
```

### 3.3 Computed

```vue
<!-- Vue 2 -->
<script>
export default {
  data() {
    return { items: [1, 2, 3] }
  },
  computed: {
    doubled() {
      return this.items.map(n => n * 2)
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
const items = ref([1, 2, 3])

const doubled = computed(() => {
  return items.value.map(n => n * 2)
})
</script>
```

### 3.4 Watch

```vue
<!-- Vue 2 -->
<script>
export default {
  data() {
    return { query: '' }
  },
  watch: {
    query(newVal, oldVal) {
      console.log(`query: ${oldVal} → ${newVal}`)
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
const query = ref('')

watch(query, (newVal, oldVal) => {
  console.log(`query: ${oldVal} → ${newVal}`)
})
</script>
```

### 3.5 Lifecycle Hooks

```vue
<!-- Vue 2 -->
<script>
export default {
  created() {
    console.log('Component created')
  },
  mounted() {
    console.log('DOM mounted')
    this.fetchData()
  },
  beforeDestroy() {
    console.log('About to destroy')
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
onMounted(() => {
  console.log('DOM mounted')
  fetchData()
})

onBeforeUnmount(() => {
  console.log('About to unmount')
})
</script>
```

### 3.6 Props

```vue
<!-- Vue 2 -->
<script>
export default {
  props: {
    title: {
      type: String,
      required: true
    },
    count: {
      type: Number,
      default: 0
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
const props = defineProps({
  title: {
    type: String,
    required: true
  },
  count: {
    type: Number,
    default: 0
  }
})

// Sử dụng
console.log(props.title)
</script>
```

### 3.7 Emit

```vue
<!-- Vue 2 -->
<script>
export default {
  emits: ['update', 'delete'],
  methods: {
    handleUpdate() {
      this.$emit('update', this.value)
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
const emit = defineEmits(['update', 'delete'])

function handleUpdate() {
  emit('update', value)
}
</script>
```

---

## 4. Vuex → Pinia

### 4.1 Store Definition

```typescript
// Vue 2 - Vuex
// store/modules/auth.js
export default {
  namespaced: true,
  state: () => ({
    user: null,
    token: null
  }),
  mutations: {
    SET_USER(state, user) {
      state.user = user
    },
    SET_TOKEN(state, token) {
      state.token = token
    }
  },
  actions: {
    async login({ commit }, credentials) {
      const response = await api.login(credentials)
      commit('SET_USER', response.user)
      commit('SET_TOKEN', response.token)
    }
  },
  getters: {
    isAuthenticated: state => !!state.token
  }
}
```

```typescript
// Nuxt 3/4 - Pinia
// app/stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref(null)
  const token = ref(null)

  // Getters
  const isAuthenticated = computed(() => !!token.value)

  // Actions
  async function login(credentials) {
    const response = await api.login(credentials)
    user.value = response.user
    token.value = response.token
  }

  return { user, token, isAuthenticated, login }
})
```

### 4.2 Sử dụng Store

```vue
<!-- Vue 2 -->
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
import { mapMutations } from 'vuex' // ❌ Mutations không nên map

export default {
  computed: {
    ...mapState('auth', ['user', 'token']),
    ...mapGetters('auth', ['isAuthenticated'])
  },
  methods: {
    ...mapActions('auth', ['login']),
    ...mapMutations('auth', ['SET_USER']) // ❌ Không nên
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
// Không cần import - auto-imported!
const authStore = useAuthStore()

// Sử dụng trực tiếp
const isLoggedIn = computed(() => authStore.isAuthenticated)

async function handleLogin() {
  await authStore.login(credentials)
}
</script>
```

### 4.3 Sự khác biệt quan trọng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUEX vs PINIA                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  VUEX:                                                             │
│  ├── Phải commit mutations                                          │
│  ├── Phải dispatch actions                                          │
│  ├── Module system phức tạp                                         │
│  └── Boilerplate nhiều                                             │
│                                                                     │
│  PINIA:                                                            │
│  ├── Gọi thẳng state/actions                                      │
│  ├── Không có mutations                                             │
│  ├── Đơn giản hơn                                                 │
│  └── TypeScript tốt hơn                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Vue Router → Nuxt Routing

### 5.1 Vue 2 Router

```typescript
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '@/views/Home.vue'
import About from '@/views/About.vue'

Vue.use(VueRouter)

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/user/:id', component: User }
]

const router = new VueRouter({
  routes,
  mode: 'history'
})
```

```vue
<!-- Vue 2 - Trong component -->
<script>
export default {
  methods: {
    goToAbout() {
      this.$router.push('/about')
    },
    getUserId() {
      return this.$route.params.id
    }
  }
}
</script>
```

### 5.2 Nuxt 3/4 - File-Based Routing

```
# Không cần cấu hình router!

app/
└── pages/
    ├── index.vue          → /
    ├── about.vue         → /about
    └── user/
        └── [id].vue      → /user/:id
```

```vue
<!-- Nuxt 3/4 - Trong component -->
<script setup>
// useRouter thay thế this.$router
const router = useRouter()

// useRoute thay thế this.$route
const route = useRoute()
const userId = route.params.id

function goToAbout() {
  router.push('/about')
}
</script>
```

### 5.3 Navigation Guards

```typescript
// Vue 2 - router/index.js
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    next('/login')
  } else {
    next()
  }
})
```

```typescript
// Nuxt 3/4 - middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    return navigateTo('/login')
  }
})
```

```vue
<!-- Nuxt 3/4 - Trong page -->
<script setup>
definePageMeta({
  middleware: 'auth'
})
</script>
```

---

## 6. Component Changes

### 6.1 v-if vs v-show

```vue
<!-- Vue 2 -->
<template>
  <div v-if="show">Nội dung</div>       <!-- Không render -->
  <div v-show="show">Nội dung</div>     <!-- Render nhưng ẩn -->
</template>
```

```vue
<!-- Nuxt 3/4 - Giống nhau -->
<template>
  <div v-if="show">Nội dung</div>
  <div v-show="show">Nội dung</div>
</template>
```

### 6.2 Event Modifiers

```vue
<!-- Vue 2 -->
<button @click.stop="handleClick">Click</button>
<input @keyup.enter="submit">
<form @submit.prevent="onSubmit">
```

```vue
<!-- Nuxt 3/4 - Giống nhau -->
<button @click.stop="handleClick">Click</button>
<input @keyup.enter="submit">
<form @submit.prevent="onSubmit">
```

### 6.3 Filters

```vue
<!-- Vue 2 - Filters -->
<template>
  <p>{{ message | uppercase }}</p>
  <p>{{ date | formatDate }}</p>
</template>

<script>
export default {
  filters: {
    uppercase(value) {
      return value.toUpperCase()
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 - Dùng functions hoặc computed -->
<template>
  <p>{{ uppercase(message) }}</p>
  <p>{{ formatDate(date) }}</p>
</template>

<script setup>
// app/utils/format.ts
function uppercase(str) {
  return str.toUpperCase()
}
</script>
```

### 6.4 Slot Syntax

```vue
<!-- Vue 2 - Named slots -->
<MyComponent>
  <template slot="header">Header</template>
  <template slot="body">Body</template>
</MyComponent>
```

```vue
<!-- Nuxt 3/4 - v-slot -->
<MyComponent>
  <template #header>Header</template>
  <template #body>Body</template>
</MyComponent>
```

---

## 7. Common Gotchas

### 7.1 this.$refs

```vue
<!-- Vue 2 -->
<script>
export default {
  methods: {
    focusInput() {
      this.$refs.input.focus()
    }
  }
}
</script>
<template>
  <input ref="input">
</template>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
const inputRef = ref(null)

function focusInput() {
  inputRef.value?.focus()
}
</script>
<template>
  <input ref="inputRef">
</template>
```

### 7.2 this.$parent

```vue
<!-- Vue 2 -->
<script>
export default {
  mounted() {
    this.$parent.doSomething()
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 - Truyền props/emits -->
<!-- Parent -->
<ChildComponent @action="handleAction" />

<!-- Child -->
<script setup>
const emit = defineEmits(['action'])
emit('action', data)
</script>
```

### 7.3 Provide/Inject

```vue
<!-- Vue 2 -->
<script>
export default {
  provide() {
    return {
      user: this.user
    }
  }
}
</script>
```

```vue
<!-- Nuxt 3/4 -->
<script setup>
import { provide } from 'vue'

const user = ref(null)
provide('user', user)
</script>
```

### 7.4 Mixins

```vue
<!-- Vue 2 - Mixin -->
<script>
import commonMixin from '@/mixins/common'

export default {
  mixins: [commonMixin]
}
</script>
```

```vue
<!-- Nuxt 3/4 - Composables -->
<script setup>
import { useCommon } from '@/composables/useCommon'

const { sharedMethod, sharedData } = useCommon()
</script>
```

---

## 🎯 Checklist Migration

```
□ Đã hiểu Composition API
□ Đã hiểu Pinia thay Vuex
□ Đã hiểu file-based routing
□ Đã đổi Options API → Composition API
□ Đã đổi Vuex stores → Pinia stores
□ Đã đổi router config → file-based routing
□ Đã đổi lifecycle hooks
□ Đã đổi this.$... → composables
```

---

## ▶️ Tiếp Theo

→ [02-nuxt3-to-nuxt4.md](02-nuxt3-to-nuxt4.md) - Migration từ Nuxt 3

hoặc → [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Quay lại học Core Concepts

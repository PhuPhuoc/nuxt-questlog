# Vue 2 → Nuxt - Hướng Dẫn Chuyển Đổi

> **Mục tiêu:** Hiểu sự khác biệt giữa Vue 2 và Nuxt và cách migrate.

## Mục lục

1. [Vue 2 vs Nuxt 3/4](#1-vue-2-vs-nuxt-34)
2. [Những thay đổi chính](#2-những-thay-đổi-chính)
3. [Migration Checklist](#3-migration-checklist)

---

## 1. Vue 2 vs Nuxt 3/4

### 1.1 So sánh nhanh

| Vue 2 | Nuxt 3/4 |
|--------|----------|
| Options API | Composition API |
| Vuex | Pinia |
| Vue Router riêng | File-based routing |
| Vue 2 core | Vue 3 core |

### 1.2 Vue 2 Components

```vue
<!-- Vue 2 - Options API -->
<script>
export default {
  data() {
    return { count: 0 }
  },
  computed: {
    doubled() {
      return this.count * 2
    }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>
```

### 1.3 Nuxt 3/4 Components

```vue
<!-- Nuxt 3/4 - Composition API -->
<script setup lang="ts">
const count = ref(0)
const doubled = computed(() => count.value * 2)

function increment() {
  count.value++
}
</script>
```

---

## 2. Những Thay Đổi Chính

### 2.1 Composition API thay vì Options API

```typescript
// Vue 2
export default {
  data() { return { name: '' } },
  created() { this.fetchData() },
  methods: { fetchData() {} }
}

// Nuxt 3/4
const name = ref('')
onMounted(() => { fetchData() })
const fetchData = async () => {}
```

### 2.2 Pinia thay vì Vuex

```typescript
// Vue 2 + Vuex
export default {
  computed: {
    user() { return this.$store.state.user }
  },
  methods: {
    login() { this.$store.dispatch('login') }
  }
}

// Nuxt 3/4 + Pinia
const authStore = useAuthStore()
const user = computed(() => authStore.user)
const login = () => authStore.login()
```

### 2.3 File-based routing

```bash
# Vue 2 - Cấu hình router
routes: [
  { path: '/', component: Home },
  { path: '/about', component: About }
]

# Nuxt 3/4 - Tạo file
pages/index.vue  → /
pages/about.vue → /about
```

---

## 3. Migration Checklist

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MIGRATION CHECKLIST                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  □ Cài đặt Nuxt 3/4 mới                                          │
│  □ Chuyển Options API → Composition API                           │
│  □ Chuyển Vuex → Pinia                                             │
│  □ Tạo file-based routes thay vì config                         │
│  □ Cập nhật plugins (navigateTo thay vì router.push)           │
│  □ Cập nhật middleware (defineNuxtRouteMiddleware)              │
│  □ Cập nhật data fetching (useFetch thay vì axios)            │
│  □ Test toàn bộ app                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tham Khảo

→ [Migration Guide](https://nuxt.com/docs/getting-started/upgrade) - Hướng dẫn chi tiết từ Nuxt docs

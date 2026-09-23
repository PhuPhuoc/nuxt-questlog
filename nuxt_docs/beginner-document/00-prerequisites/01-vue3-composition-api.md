# Vue 3 Composition API - Kiến Thức Cần Biết Trước Khi Học Nuxt

> **Quan trọng:** Nuxt 4 sử dụng Vue 3 Composition API làm mặc định. Bạn **PHẢI** hiểu phần này trước khi học Nuxt.

## Mục lục

1. [Tại sao phải học Composition API?](#1-tại-sao-phải-học-composition-api)
2. [Script Setup là gì?](#2-script-setup-là-gì)
3. [ref() - Tạo Reactive Data](#3-ref---tạo-reactive-data)
4. [computed() - Tính Toán Reactive](#4-computed---tính-toán-reactive)
5. [watch() - Theo Dõi Thay Đổi](#5-watch---theo-dõi-thay-đổi)
6. [Lifecycle Hooks](#6-lifecycle-hooks)
7. [Props và Emits](#7-props-và-emits)
8. [Provide/Inject](#8-provideinject)

---

## 1. Tại Sao Phải Học Composition API?

### 1.1 So Sánh Options API vs Composition API

```
┌─────────────────────────────────────────────────────────────────────┐
│                    OPTIONS API (Vue 2) vs COMPOSITION API (Vue 3)        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  OPTIONS API - Cách cũ                                            │
│  ───────────────────────                                            │
│  ├── data() → State                                               │
│  ├── computed: {} → Getters                                        │
│  ├── methods: {} → Functions                                       │
│  ├── watch: {} → Watchers                                          │
│  └── Code NẰM RỜI nhau theo loại, không theo tính năng          │
│                                                                     │
│  COMPOSITION API - Cách mới (Khuyến nghị)                          │
│  ──────────────────────────────────────                             │
│  ├── ref() / reactive() → State                                    │
│  ├── computed() → Getters                                         │
│  ├── function → Functions                                         │
│  ├── watch() / watchEffect() → Watchers                           │
│  └── Code NHÓM THEO TÍNH NĂNG, dễ tái sử dụng                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2 Ví dụ So Sánh

```vue
<!-- Vue 2 - Options API -->
<script>
export default {
  data() {
    return { count: 0, message: 'Hello' }
  },
  computed: {
    doubled() { return this.count * 2 }
  },
  methods: {
    increment() { this.count++ }
  },
  mounted() {
    console.log('Mounted!')
  }
}
</script>
```

```vue
<!-- Vue 3 - Composition API với <script setup> -->
<script setup lang="ts">
// ✅ Code NHÓM THEO TÍNH NĂNG, không phải theo loại
const count = ref(0)
const message = ref('Hello')

const doubled = computed(() => count.value * 2)

function increment() {
  count.value++
}

onMounted(() => {
  console.log('Mounted!')
})
</script>
```

### 1.3 Lợi ích của Composition API

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LỢI ÍCH CỦA COMPOSITION API                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Logic Tái Sử Dụng Được                                      │
│     ├── Tách logic ra composables/functions                        │
│     └── Import vào nhiều components                               │
│                                                                     │
│  ✅ Code Tổ Chức Tốt Hơn                                        │
│     ├── Nhóm code theo tính năng, không phải theo loại          │
│     └── Dễ đọc hơn khi component phức tạp                       │
│                                                                     │
│  ✅ TypeScript Tốt Hơn                                           │
│     ├── Type inference tự động                                    │
│     └── Không cần this.$...                                       │
│                                                                     │
│  ✅ Bundle Size Nhỏ Hơn                                          │
│     └── Chỉ import những gì cần dùng                            │
│                                                                     │
│  ✅ ĐƯỢC KHUYẾN NGHỊ TRONG NUXT 4                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Script Setup là gì?

### 2.1 Có Sẵn Hay Cần Custom?

**`<script setup>` LÀ CÚ PHÁP CÓ SẴN CỦA VUE 3!**

- Không cần cài đặt gì thêm
- Vue 3 hỗ trợ sẵn
- Nuxt 4 auto-import `<script setup>`

### 2.2 So Sánh Cú Pháp

```vue
<!-- CÚ PHÁP CŨ: <script> thông thường -->
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    
    function increment() {
      count.value++
    }
    
    return { count, increment }
  }
}
</script>
```

```vue
<!-- CÚ PHÁP MỚI: <script setup> (Khuyến nghị) -->
<script setup lang="ts">
// ✅ Không cần return - tự động expose ra template
const count = ref(0)

function increment() {
  count.value++
}
// count và increment tự động có trong template
</script>
```

### 2.3 Lợi ích của `<script setup>`

```
┌─────────────────────────────────────────────────────────────────────┐
│                    <script setup> LỢI ÍCH                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✓ Tự động import những gì dùng trong template                   │
│  ✓ Không cần return object                                        │
│  ✓ Props và emits tự động define                                  │
│  ✓ Code ngắn hơn, rõ ràng hơn                                    │
│  ✓ Performance tốt hơn (biên dịch ra setup() efficient hơn)      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. ref() - Tạo Reactive Data

### 3.1 Có Sẵn Hay Cần Custom?

**`ref()` LÀ COMPOSABLE CÓ SẴN CỦA VUE 3!**

- Auto-imported trong Nuxt 4
- Không cần import từ 'vue'

### 3.2 Cách Hoạt Động

```vue
<script setup lang="ts">
// Tạo reactive state
const count = ref(0)
const name = ref('Nam')
const isActive = ref(false)
const user = ref({ name: 'Nam', age: 25 })
</script>

<template>
  <!-- Template: KHÔNG cần .value (tự unwrap) -->
  <p>{{ count }}</p>           <!-- Hiển thị: 0 -->
  <p>{{ name }}</p>             <!-- Hiển thị: Nam -->
  <p>{{ isActive }}</p>         <!-- Hiển thị: false -->
  <p>{{ user.name }}</p>        <!-- Hiển thị: Nam -->
</template>
```

### 3.3 Đọc và Ghi Giá Trị

```vue
<script setup lang="ts">
const count = ref(0)

console.log(count.value)  // 0 - Lấy giá trị
count.value = 10          // Gán giá trị
count.value++             // Tăng giá trị
</script>
```

### 3.4 Ví dụ: Counter Component

```vue
<script setup lang="ts">
// State
const count = ref(0)

// Computed
const doubled = computed(() => count.value * 2)

// Methods
function increment() {
  count.value++
}

function decrement() {
  count.value--
}

function reset() {
  count.value = 0
}
</script>

<template>
  <div class="counter">
    <h2>{{ count }}</h2>
    <p>Double: {{ doubled }}</p>

    <button @click="decrement">-</button>
    <button @click="reset">Reset</button>
    <button @click="increment">+</button>
  </div>
</template>
```

### 3.5 reactive() vs ref()

```vue
<script setup lang="ts">
// ref() - cho MỌI loại dữ liệu
const count = ref(0)                    // number
const name = ref('Nam')                 // string
const user = ref({ name: 'Nam' })      // object (cần .value)

// reactive() - CHỈ cho objects (không cần .value)
const state = reactive({
  count: 0,
  name: 'Nam',
  isActive: false
})

state.count++  // ✅ Trực tiếp, không cần .value
</script>
```

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ref() vs reactive()                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ref():                                                          │
│  ├── Dùng cho MỌI loại dữ liệu (string, number, object, array)   │
│  ├── Cần .value để đọc/ghi trong script                          │
│  ├── Template tự unwrap - không cần .value                        │
│  └── Khuyến nghị: Dùng ref() CHO MỌI THỨ                       │
│                                                                     │
│  reactive():                                                     │
│  ├── Chỉ dùng cho objects                                        │
│  ├── Không cần .value                                            │
│  ├── Có thể destructure mà không mất reactivity                  │
│  └── Khuyến nghị: Dùng khi cần destructure object               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. computed() - Tính Toán Reactive

### 4.1 Có Sẵn Hay Cần Custom?

**`computed()` LÀ COMPOSABLE CÓ SẴN CỦA VUE 3!**

- Auto-imported trong Nuxt 4
- Tự động cache kết quả
- Tự động update khi dependencies thay đổi

### 4.2 Ví dụ: Todo List

```vue
<script setup lang="ts">
const todos = ref([
  { id: 1, text: 'Học Vue', done: true },
  { id: 2, text: 'Học Nuxt', done: false },
  { id: 3, text: 'Làm project', done: false }
])

// Computed: những việc chưa hoàn thành
const pendingTodos = computed(() => {
  return todos.value.filter(todo => !todo.done)
})

// Computed: những việc đã hoàn thành
const completedTodos = computed(() => {
  return todos.value.filter(todo => todo.done)
})

// Computed: số lượng
const totalCount = computed(() => todos.value.length)
const completedCount = computed(() => completedTodos.value.length)

// Computed: phần trăm hoàn thành
const progressPercent = computed(() => {
  if (totalCount.value === 0) return 0
  return Math.round((completedCount.value / totalCount.value) * 100)
})

function addTodo(text: string) {
  todos.value.push({
    id: Date.now(),
    text,
    done: false
  })
}

function toggleTodo(id: number) {
  const todo = todos.value.find(t => t.id === id)
  if (todo) todo.done = !todo.done
}
</script>

<template>
  <div class="todo-app">
    <h1>Todo App</h1>

    <!-- Stats -->
    <p>Hoàn thành: {{ completedCount }}/{{ totalCount }} ({{ progressPercent }}%)</p>

    <!-- Pending -->
    <h2>Cần làm ({{ pendingTodos.length }})</h2>
    <ul>
      <li v-for="todo in pendingTodos" :key="todo.id">
        <input
          type="checkbox"
          :checked="todo.done"
          @change="toggleTodo(todo.id)"
        >
        {{ todo.text }}
      </li>
    </ul>

    <!-- Completed -->
    <h2>Đã hoàn thành ({{ completedCount }})</h2>
    <ul>
      <li v-for="todo in completedTodos" :key="todo.id" class="done">
        <input
          type="checkbox"
          :checked="todo.done"
          @change="toggleTodo(todo.id)"
        >
        {{ todo.text }}
      </li>
    </ul>
  </div>
</template>
```

---

## 5. watch() - Theo Dõi Thay Đổi

### 5.1 Có Sẵn Hay Cần Custom?

**`watch()` VÀ `watchEffect()` LÀ COMPOSABLES CÓ SẴN CỦA VUE 3!**

- Auto-imported trong Nuxt 4
- `watch()` - Chỉ theo dõi NHỮNG BIẾN CỤ THỂ
- `watchEffect()` - Tự động track TẤT CẢ dependencies

### 5.2 So Sánh watch() vs watchEffect()

```
┌─────────────────────────────────────────────────────────────────────┐
│                    watch() vs watchEffect()                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  watch()                                                            │
│  ─────────                                                          │
│  • Chỉ theo dõi NHỮNG BIẾN CỤ THỂ                               │
│  • Có quyền kiểm soát khi nào chạy                               │
│  • Có access vào giá trị TRƯỚC và SAU thay đổi                   │
│  • Ví dụ: Debounce search input                                    │
│                                                                     │
│  watchEffect()                                                      │
│  ─────────────                                                      │
│  • Tự động track TẤT CẢ reactive dependencies                    │
│  • Chạy NGAY LẬP TỨC khi được tạo                               │
│  • Không biết giá trị cũ                                          │
│  • Ví dụ: Log khi bất kỳ biến nào thay đổi                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.3 Ví dụ: Debounced Search

```vue
<script setup lang="ts">
const searchQuery = ref('')
const searchResults = ref<string[]>([])

// Debounce: Đợi 500ms sau khi user ngừng gõ
let timeoutId: ReturnType<typeof setTimeout> | null = null

watch(searchQuery, (newQuery) => {
  // Clear timeout trước đó
  if (timeoutId) clearTimeout(timeoutId)

  if (!newQuery.trim()) {
    searchResults.value = []
    return
  }

  // Đợi 500ms
  timeoutId = setTimeout(async () => {
    // Gọi API search
    searchResults.value = await searchAPI(newQuery)
  }, 500)
})

// Mock API
async function searchAPI(query: string): Promise<string[]> {
  // Simulate API call
  return [`Kết quả cho "${query}" 1`, `Kết quả cho "${query}" 2`]
}
</script>

<template>
  <div>
    <input v-model="searchQuery" placeholder="Tìm kiếm...">

    <ul v-if="searchResults.length">
      <li v-for="result in searchResults" :key="result">
        {{ result }}
      </li>
    </ul>
    <p v-else-if="searchQuery">Đang tìm...</p>
  </div>
</template>
```

---

## 6. Lifecycle Hooks

### 6.1 Có Sẵn Hay Cần Custom?

**LIFECYCLE HOOKS LÀ CÓ SẴN TRONG VUE 3!**

- Auto-imported trong Nuxt 4
- Vue 3 đổi tên: `beforeDestroy` → `beforeUnmount`, `destroyed` → `unmounted`

### 6.2 Danh Sách Lifecycle Hooks

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE 3 LIFECYCLE HOOKS                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Creation                                                          │
│  ├── setup()           ← Chạy ĐẦU TIÊN                           │
│  └── (onBeforeCreate, onCreated - Không dùng trong <script setup>) │
│                                                                     │
│  Mounting                                                         │
│  ├── onBeforeMount() ← Trước khi DOM được mount                  │
│  └── onMounted()    ← SAU KHI DOM được mount ✅ HAY DÙNG      │
│                                                                     │
│  Updating                                                         │
│  ├── onBeforeUpdate() ← Trước khi re-render                      │
│  └── onUpdated()     ← SAU KHI re-render ✅ HAY DÙNG          │
│                                                                     │
│  Unmounting                                                       │
│  ├── onBeforeUnmount() ← Trước khi unmount                        │
│  └── onUnmounted()   ← SAU KHI unmount ✅ HAY DÙNG            │
│                                                                     │
│  Error Handling                                                   │
│  └── onErrorCaptured() ← Khi có lỗi trong component               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.3 Ví dụ: Event Listener Cleanup

```vue
<script setup lang="ts">
const mousePosition = ref({ x: 0, y: 0 })

function handleMouseMove(event: MouseEvent) {
  mousePosition.value = {
    x: event.clientX,
    y: event.clientY
  }
}

onMounted(() => {
  // Add event listener
  window.addEventListener('mousemove', handleMouseMove)
})

onUnmounted(() => {
  // Cleanup - RẤT QUAN TRỌNG! Tránh memory leaks
  window.removeEventListener('mousemove', handleMouseMove)
})
</script>

<template>
  <div>
    <p>Mouse position: {{ mousePosition.x }}, {{ mousePosition.y }}</p>
  </div>
</template>
```

---

## 7. Props và Emits

### 7.1 Có Sẵn Hay Cần Custom?

**`defineProps()` VÀ `defineEmits()` LÀ MACROS CÓ SẴN CỦA VUE 3!**

- Không cần import
- Chỉ hoạt động trong `<script setup>`
- TypeScript-first

### 7.2 defineProps - TypeScript Style

```vue
<!-- UserCard.vue -->
<script setup lang="ts">
interface User {
  id: number
  name: string
  email: string
  avatar?: string
}

// Định nghĩa props với TypeScript
const props = defineProps<{
  user: User
  isCompact?: boolean
}>()

// Props là readonly!
console.log(props.user.name)
</script>

<template>
  <div :class="{ compact: props.isCompact }">
    <img v-if="props.user.avatar" :src="props.user.avatar" :alt="props.user.name">
    <h3>{{ props.user.name }}</h3>
    <p>{{ props.user.email }}</p>
  </div>
</template>
```

### 7.3 defineEmits - TypeScript Style

```vue
<!-- CustomButton.vue -->
<script setup lang="ts">
const emit = defineEmits<{
  click: [event: MouseEvent]
  submit: [data: { name: string; value: string }]
}>()

function handleClick() {
  emit('click', new MouseEvent('click'))
}

function handleSubmit() {
  emit('submit', { name: 'test', value: '123' })
}
</script>

<template>
  <button @click="handleClick">Click</button>
  <button @click="handleSubmit">Submit</button>
</template>
```

### 7.4 Sử Dụng với Default Values

```vue
<script setup lang="ts">
interface Props {
  title: string
  count?: number
  isActive?: boolean
}

// Default values với withDefaults
const props = withDefaults(defineProps<Props>(), {
  count: 0,
  isActive: false
})
</script>
```

---

## 8. Provide/Inject

### 8.1 Có Sẵn Hay Cần Custom?

**`provide()` VÀ `inject()` LÀ COMPOSABLES CÓ SẴN CỦA VUE 3!**

- Auto-imported trong Nuxt 4
- Dùng khi truyền data qua NHIỀU CẤP components

### 8.2 Sơ Đồ Provide/Inject

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PROVIDE / INJECT - KHI NÀO DÙNG?                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Dùng khi: Truyền data qua NHIỀU CẤP components                   │
│                                                                     │
│  Ví dụ: Theme (light/dark)                                         │
│  ├── App.vue (Provide: theme)                                      │
│  │   ├── Header.vue (Inject: theme)                                │
│  │   ├── Sidebar.vue (Inject: theme)                              │
│  │   └── Content.vue                                               │
│  │       ├── Post.vue (Inject: theme)                              │
│  │       └── Comment.vue (Inject: theme)                          │
│                                                                     │
│  Thay vì: Prop drilling (truyền qua nhiều cấp)                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 8.3 Provide

```vue
<!-- App.vue - Cung cấp theme -->
<script setup lang="ts">
import { provide, ref } from 'vue'

const theme = ref<'light' | 'dark'>('light')

// Provide cho tất cả descendants
provide('theme', theme)

// Provide một object (nhiều giá trị)
provide('user', {
  name: 'Nam',
  email: 'nam@example.com',
  role: 'admin'
})

function toggleTheme() {
  theme.value = theme.value === 'light' ? 'dark' : 'light'
}

provide('toggleTheme', toggleTheme)
</script>
```

### 8.4 Inject

```vue
<!-- Sidebar.vue - Nhận theme -->
<script setup lang="ts">
// Inject với default value
const theme = inject('theme', ref('light'))
</script>

<template>
  <aside :class="theme">
    Sidebar content
  </aside>
</template>
```

### 8.5 Typed Inject (TypeScript)

```vue
<script setup lang="ts">
// Type-safe inject với InjectionKey
import type { InjectionKey } from 'vue'

interface Theme {
  value: 'light' | 'dark'
  toggle: () => void
}

const ThemeKey: InjectionKey<Theme> = Symbol('theme')

// Provide
provide(ThemeKey, {
  value: theme.value,
  toggle: toggleTheme
})

// Inject
const theme = inject(ThemeKey)
if (theme) {
  console.log(theme.value)
}
</script>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CHECKLIST - ĐÃ HIỂU CHƯA?                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  □ ref() tạo reactive data (dùng .value trong script)             │
│  □ reactive() tạo reactive object (không cần .value)               │
│  □ .value để access/modify trong script (không cần trong template)│
│  □ computed() tạo computed property                                 │
│  □ watch() theo dõi biến cụ thể                                   │
│  □ watchEffect() tự động track dependencies                       │
│  □ onMounted/onUnmounted cho lifecycle                             │
│  □ defineProps() định nghĩa props (TypeScript style)              │
│  □ defineEmits() định nghĩa emits                                │
│  □ provide/inject cho prop drilling                                │
│  □ <script setup> là cú pháp được khuyến nghị                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [02-node-basics.md](02-node-basics.md) - Kiến thức Node.js cơ bản

hoặc → [Bỏ qua qua phần khác nếu đã hiểu rõ](01-getting-started/01-installation.md)

---

*Nếu bạn thấy phần này khó, hãy học thêm tại [Vue.js.org](https://vuejs.org/guide/quick-start.html)*

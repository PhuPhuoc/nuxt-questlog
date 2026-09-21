# Vue 3 Composition API - Kiến Thức Cần Biết Trước Khi Học Nuxt

> **Quan trọng:** Nuxt sử dụng Vue 3 Composition API làm mặc định. Bạn PHẢI hiểu phần này trước khi học Nuxt.

## Mục lục

1. [Tại sao phải học Composition API?](#1-tại-sao-phải-học-composition-api)
2. [Script Setup là gì?](#2-script-setup-là-gì)
3. [ref() - Tạo reactive data](#3-ref---tạo-reactive-data)
4. [computed() - Tính toán reactive](#4-computed---tính-toán-reactive)
5. [watch() - Theo dõi thay đổi](#5-watch---theo-dõi-thay-đổi)
6. [Lifecycle Hooks](#6-lifecycle-hooks)
7. [Props và Emits](#7-props-và-emits)
8. [Provide/Inject](#8-provideinject)
9. [So sánh Options API vs Composition API](#9-so-sánh-options-api-vs-composition-api)

---

## 1. Tại Sao Phải Học Composition API?

### Options API (Vue 2) - Cách cũ

```vue
<!-- Vue 2 / Options API -->
<template>
  <button @click="increment">{{ count }}</button>
</template>

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

### Composition API (Vue 3) - Cách mới

```vue
<!-- Vue 3 / Composition API -->
<template>
  <button @click="increment">{{ count }}</button>
</template>

<script setup>
// Logic ở cùng 1 chỗ!
const count = ref(0)  // Reactive state

function increment() {
  count.value++  // .value để access/ref
}
</script>
```

### Tại sao Composition API TỐT HƠN?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPOSITION API - LỢI ÍCH                           │
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
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Script Setup là gì?

### `<script setup>` là gì?

`<script setup>` là cú pháp **đƯỢC KHUYẾN NGHỊ** trong Vue 3 để viết Composition API:

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
<!-- CÚ PHÁP MỚI: <script setup> -->
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}

// KHÔNG CẦN return! Tự động expose ra template
</script>
```

### Lợi ích của `<script setup>`

```vue
<script setup>
/*
  ✓ Tự động import những gì dùng trong template
  ✓ Không cần return object
  ✓ Props và emits tự động define
  ✓ Code ngắn hơn, rõ ràng hơn
*/
</script>
```

### So sánh nhanh

| Tính năng | `<script>` thường | `<script setup>` |
|-----------|-------------------|-----------------|
| Import | `import { ref }` | `import { ref }` |
| Return | Phải return | Tự động |
| Props | `props: {}` | `defineProps()` |
| Emits | `emits: []` | `defineEmits()` |
| Code | Dài hơn | Ngắn hơn |

---

## 3. ref() - Tạo Reactive Data

### ref() là gì?

`ref()` tạo một **reactive reference** đến một giá trị:

```javascript
import { ref } from 'vue'

// Tạo reactive state
const count = ref(0)
const name = ref('Nam')
const isActive = ref(false)
const user = ref({ name: 'Nam', age: 25 })
```

### Access và Modify giá trị

```javascript
const count = ref(0)

console.log(count.value)  // 0 - Lấy giá trị
count.value = 10          // Gán giá trị
count.value++             // Tăng giá trị

// Template: KHÔNG cần .value (tự unwrap)
```

### Trong Template - Tự động "unwrap"

```vue
<script setup>
const count = ref(5)
const message = ref('Hello')
</script>

<template>
  <!-- KHÔNG cần count.value trong template! -->
  <p>{{ count }}</p>      <!-- Hiển thị: 5 -->
  <p>{{ message }}</p>    <!-- Hiển thị: Hello -->
</template>
```

### Ví dụ: Counter Component

```vue
<script setup>
import { ref } from 'vue'

// Tạo state
const count = ref(0)

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

    <button @click="decrement">-</button>
    <button @click="reset">Reset</button>
    <button @click="increment">+</button>
  </div>
</template>
```

### Reactive với Objects - reactive()

```javascript
import { ref, reactive } from 'vue'

// ref() cho primitive values
const name = ref('Nam')
const age = ref(25)

// reactive() cho objects - KHÔNG cần .value
const user = reactive({
  name: 'Nam',
  age: 25,
  email: 'nam@example.com'
})

user.name = 'Minh'  // ✅ Đổi trực tiếp
user.age = 30       // ✅ Đổi trực tiếp
```

### ref vs reactive

```javascript
// ref() - cho MỌI loại dữ liệu
const count = ref(0)                    // number
const name = ref('Nam')                 // string
const user = ref({ name: 'Nam' })      // object (cần .value)
const items = ref([])                  // array (cần .value)

// reactive() - CHỈ cho objects
const user = reactive({ name: 'Nam' })
user.name = 'Minh'  // ✅ Trực tiếp

// KHI NÀO DÙNG GÌ?
// → Dùng ref() cho mọi thứ (ĐƠN GIẢN HƠN)
// → reactive() khi cần destructure object
```

---

## 4. computed() - Tính Toán Reactive

### computed() là gì?

`computed()` tạo một **reactive computed property** - tự động cập nhật khi dependencies thay đổi:

```javascript
import { ref, computed } from 'vue'

const firstName = ref('Nguyen')
const lastName = ref('Nam')

// Computed property - tự động update khi firstName/lastName thay đổi
const fullName = computed(() => {
  return `${lastName.value} ${firstName.value}`
})

console.log(fullName.value)  // "Nguyen Nam"

firstName.value = 'Minh'
console.log(fullName.value)  // "Nguyen Minh" (tự động cập nhật!)
```

### Ví dụ: Todo List với computed

```vue
<script setup>
import { ref, computed } from 'vue'

const todos = ref([
  { id: 1, text: 'Học Vue', done: true },
  { id: 2, text: 'Học Nuxt', done: false },
  { id: 3, text: 'Làm project', done: false }
])

const newTodo = ref('')

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

function addTodo() {
  if (!newTodo.value.trim()) return

  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    done: false
  })
  newTodo.value = ''
}

function toggleTodo(id) {
  const todo = todos.value.find(t => t.id === id)
  if (todo) todo.done = !todo.done
}
</script>

<template>
  <div class="todo-app">
    <h1>Todo App</h1>

    <!-- Stats -->
    <p>Hoàn thành: {{ completedCount }}/{{ totalCount }}</p>

    <!-- Add todo -->
    <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Thêm việc mới...">
    <button @click="addTodo">Thêm</button>

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

### watch() là gì?

`watch()` theo dõi một reactive value và chạy callback khi nó thay đổi:

```javascript
import { ref, watch } from 'vue'

const count = ref(0)

// Theo dõi count
watch(count, (newValue, oldValue) => {
  console.log(`count thay đổi: ${oldValue} → ${newValue}`)
})

count.value++  // Console: "count thay đổi: 0 → 1"
count.value++  // Console: "count thay đổi: 1 → 2"
```

### watchEffect() - Tự động track dependencies

```javascript
import { ref, watchEffect } from 'vue'

const firstName = ref('Nam')
const lastName = ref('Nguyen')

// Tự động track cả firstName và lastName
watchEffect(() => {
  console.log(`Tên: ${lastName.value} ${firstName.value}`)
})

firstName.value = 'Minh'  // Console: "Tên: Nguyen Minh"
lastName.value = 'Tran'   // Console: "Tên: Tran Minh"
```

### Khi nào dùng watch vs watchEffect?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    WATCH vs WATCHEFFECT                                │
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

### Ví dụ thực tế: Debounced Search

```vue
<script setup>
import { ref, watch } from 'vue'

const searchQuery = ref('')
const searchResults = ref([])

// Debounce: Đợi 500ms sau khi user ngừng gõ
let timeoutId = null

watch(searchQuery, (newQuery) => {
  // Clear timeout trước đó
  clearTimeout(timeoutId)

  if (!newQuery.trim()) {
    searchResults.value = []
    return
  }

  // Đợi 500ms
  timeoutId = setTimeout(async () => {
    // Gọi API search
    const results = await searchAPI(newQuery)
    searchResults.value = results
  }, 500)
})
</script>

<template>
  <div>
    <input v-model="searchQuery" placeholder="Tìm kiếm...">

    <ul v-if="searchResults.length">
      <li v-for="result in searchResults" :key="result.id">
        {{ result.name }}
      </li>
    </ul>
  </div>
</template>
```

---

## 6. Lifecycle Hooks

### Vue 3 Lifecycle Hooks

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VUE 3 LIFECYCLE HOOKS                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Creation                                                          │
│  ├── setup()           ← Chạy ĐẦU TIÊN                           │
│  ├── onBeforeCreate()  ← Trước khi setup chạy (DÙNG RỒI)        │
│  └── onCreated()       ← Sau setup (DÙNG RỒI - logic vào setup)   │
│                                                                     │
│  Mounting                                                         │
│  ├── onBeforeMount()  ← Trước khi DOM được mount                  │
│  └── onMounted()      ← SAU KHI DOM được mount ✅ HAY DÙNG      │
│                                                                     │
│  Updating                                                         │
│  ├── onBeforeUpdate() ← Trước khi re-render                      │
│  └── onUpdated()      ← SAU KHI re-render                        │
│                                                                     │
│  Unmounting                                                       │
│  ├── onBeforeUnmount() ← Trước khi unmount                        │
│  └── onUnmounted()    ← SAU KHI unmount ✅ HAY DÙNG            │
│                                                                     │
│  Error Handling                                                   │
│  └── onErrorCaptured() ← Khi có lỗi trong component               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Cách sử dụng trong `<script setup>`

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const count = ref(0)

// Chạy khi component được mount vào DOM
onMounted(() => {
  console.log('Component mounted!')

  // Ví dụ: Fetch data khi mount
  fetchData()

  // Ví dụ: Add event listener
  window.addEventListener('resize', handleResize)
})

// Chạy khi component bị unmount khỏi DOM
onUnmounted(() => {
  console.log('Component unmounted!')

  // Cleanup - QUAN TRỌNG!
  window.removeEventListener('resize', handleResize)
})

// Các hooks khác
onBeforeMount(() => {
  // Trước khi mount
})

onUpdated(() => {
  // Sau khi update
})

onBeforeUnmount(() => {
  // Trước khi unmount
})
</script>
```

### Ví dụ: Event Listener Cleanup

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const mousePosition = ref({ x: 0, y: 0 })

function handleMouseMove(event) {
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
  // Cleanup - RẤT QUAN TRỌNG!
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

### Props - Truyền data từ Parent → Child

```vue
<!-- Parent Component -->
<template>
  <UserCard
    name="Nguyen Nam"
    email="nam@example.com"
    :age="25"
    :is-active="true"
  />
</template>

<!-- Child Component: UserCard.vue -->
<script setup>
// Định nghĩa props với defineProps
const props = defineProps({
  name: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true
  },
  age: {
    type: Number,
    default: 0
  },
  isActive: {
    type: Boolean,
    default: false
  }
})

// Props là readonly!
console.log(props.name)  // "Nguyen Nam"
</script>

<template>
  <div class="user-card">
    <h3>{{ name }}</h3>
    <p>{{ email }}</p>
    <p>Tuổi: {{ age }}</p>
    <span v-if="isActive">Đang hoạt động</span>
  </div>
</template>
```

### Type-only Props (TypeScript)

```typescript
// Dùng TypeScript - ngắn gọn hơn
const props = defineProps<{
  name: string
  email: string
  age?: number      // Optional với ?
  isActive?: boolean
}>()

// Default values với withDefaults
const props = withDefaults(defineProps<{
  name: string
  email: string
  age?: number
  isActive?: boolean
}>(), {
  age: 0,
  isActive: false
})
```

### Emits - Gửi event từ Child → Parent

```vue
<!-- Child Component: CustomButton.vue -->
<script setup>
const emit = defineEmits(['click', 'submit'])

// Gửi event lên parent
function handleClick() {
  emit('click', 'Button clicked!')
}

function handleSubmit() {
  const data = { name: 'Nam', age: 25 }
  emit('submit', data)
}
</script>

<template>
  <button @click="handleClick">Click me</button>
  <button @click="handleSubmit">Submit</button>
</template>
```

```vue
<!-- Parent Component -->
<template>
  <CustomButton
    @click="handleClick"
    @submit="handleSubmit"
  />
</template>

<script setup>
function handleClick(message) {
  console.log(message)  // "Button clicked!"
}

function handleSubmit(data) {
  console.log(data)  // { name: 'Nam', age: 25 }
}
</script>
```

### Props + Emits = Component Communication

```vue
<!-- Complete Example: TodoItem.vue -->
<script setup>
// Props
defineProps<{
  todo: {
    id: number
    text: string
    done: boolean
  }
}>()

// Emits
const emit = defineEmits<{
  toggle: [id: number]
  delete: [id: number]
}>()

function handleToggle() {
  emit('toggle', props.todo.id)
}

function handleDelete() {
  emit('delete', props.todo.id)
}
</script>

<template>
  <li :class="{ done: todo.done }">
    <input
      type="checkbox"
      :checked="todo.done"
      @change="handleToggle"
    >
    <span>{{ todo.text }}</span>
    <button @click="handleDelete">Xóa</button>
  </li>
</template>
```

---

## 8. Provide/Inject

### Khi nào dùng Provide/Inject?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PROVIDE / INJECT                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Dùng khi: Truyền data qua NHIỀU CẤP components                   │
│                                                                     │
│  Ví dụ: Theme (light/dark)                                         │
│  ├── App (Provide: theme)                                          │
│  │   ├── Header (Inject: theme)                                    │
│  │   ├── Sidebar (Inject: theme)                                   │
│  │   └── Content                                                   │
│  │       ├── Post (Inject: theme)                                  │
│  │       └── Comment (Inject: theme)                              │
│                                                                     │
│  Thay vì: Prop drilling (truyền qua nhiều cấp)                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Provide

```vue
<!-- App.vue - Cung cấp theme -->
<script setup>
import { ref, provide } from 'vue'

const theme = ref('light')

// Provide cho tất cả descendants
provide('theme', theme)

// Provide một object (nhiều giá trị)
provide('user', {
  name: 'Nam',
  email: 'nam@example.com',
  role: 'admin'
})
</script>

<template>
  <div :class="theme">
    <Header />
    <Sidebar />
    <Content />
  </div>
</template>
```

### Inject

```vue
<!-- Sidebar.vue - Nhận theme -->
<script setup>
import { inject } from 'vue'

// Inject với default value
const theme = inject('theme', ref('light'))
</script>

<template>
  <aside :class="theme">
    Sidebar content
  </aside>
</template>
```

```vue
<!-- Comment.vue - Nhận cả theme và user -->
<script setup>
import { inject } from 'vue'

const theme = inject('theme')
const user = inject('user')

function toggleTheme() {
  theme.value = theme.value === 'light' ? 'dark' : 'light'
}
</script>

<template>
  <div :class="theme">
    <p>Comment by {{ user.name }}</p>
    <button @click="toggleTheme">Toggle Theme</button>
  </div>
</template>
```

---

## 9. So Sánh Options API vs Composition API

### So sánh trực tiếp

```
┌─────────────────────────────────────────────────────────────────────┐
│                    OPTIONS API vs COMPOSITION API                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  OPTIONS API (Vue 2)                                                │
│  ───────────────────                                                │
│  data()            →  ref(), reactive()                           │
│  computed: {}      →  computed()                                  │
│  methods: {}       →  function declarations                       │
│  watch: {}        →  watch(), watchEffect()                      │
│  mounted()         →  onMounted()                                  │
│  props: {}        →  defineProps()                                │
│  emits: []        →  defineEmits()                               │
│  inject: []        →  inject()                                    │
│                                                                     │
│  COMPOSITION API (Vue 3)                                            │
│  ─────────────────────────                                          │
│  • Logic nhóm theo tính năng                                      │
│  • Dễ tái sử dụng (composables)                                  │
│  • TypeScript tốt hơn                                              │
│  • Bundle size nhỏ hơn                                              │
│  • ĐƯỢC KHUYẾN NGHỊ TRONG NUXT                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Ví dụ cùng một component

```vue
<!-- OPTIONS API (Vue 2 style) -->
<script>
export default {
  data() {
    return {
      count: 0,
      message: 'Hello'
    }
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
  },
  mounted() {
    console.log('Mounted')
  }
}
</script>

<!-- COMPOSITION API (Vue 3 - Nuxt style) -->
<script setup>
import { ref, computed, onMounted } from 'vue'

const count = ref(0)
const message = ref('Hello')

const doubled = computed(() => count.value * 2)

function increment() {
  count.value++
}

onMounted(() => {
  console.log('Mounted')
})
</script>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CHECKLIST - ĐÃ HIỂU CHƯA?                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  □ ref() tạo reactive data cho primitive values                  │
│  □ reactive() tạo reactive data cho objects                       │
│  □ .value để access/modify trong script (không cần trong template)│
│  □ computed() tạo computed property                                │
│  □ watch() theo dõi sự thay đổi của một biến                     │
│  □ onMounted/onUnmounted cho lifecycle                             │
│  □ defineProps() định nghĩa props                                 │
│  □ defineEmits() định nghĩa emits                                │
│  □ provide/inject cho prop drilling                                │
│  □ <script setup> là cú pháp được khuyến nghị                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Bài Tập Kiểm Tra

### Bài 1: Counter với computed

Tạo component với:
- `count` (ref)
- `doubled` (computed = count × 2)
- `squared` (computed = count²)
- 2 buttons: +1 và -1

### Bài 2: User Profile

Tạo component với:
- Props: `name`, `email`, `avatar`
- Computed: `initials` (2 chữ cái đầu của tên)
- Emit: `edit` khi click button

### Bài 3: Watch Effect

Tạo component với:
- Input để search
- `watchEffect()` để log mỗi khi input thay đổi

---

## ▶️ Tiếp Theo

→ [02-node-basics.md](02-node-basics.md) - Kiến thức Node.js cơ bản

hoặc → [Bỏ qua qua phần khác nếu đã hiểu rõ](01-getting-started/01-installation.md)

---

*Nếu bạn thấy phần này khó, hãy học thêm tại [Vue.js.org](https://vuejs.org/guide/quick-start.html)*

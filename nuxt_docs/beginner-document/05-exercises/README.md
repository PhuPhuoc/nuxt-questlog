# Bài Tập Cho Người Mới

> **Mức độ:** Người mới bắt đầu | **Thời gian:** 30-60 phút mỗi bài

## Mục lục

1. [Bài 1: Hello World](#bài-1-hello-world)
2. [Bài 2: Counter App](#bài-2-counter-app)
3. [Bài 3: Todo List Cơ Bản](#bài-3-todo-list-cơ-bản)
4. [Bài 4: Profile Card](#bài-4-profile-card)
5. [Bài 5: Navigation](#bài-5-navigation)

---

## Bài 1: Hello World

### Mục tiêu
- Hiểu cách tạo page trong Nuxt
- Hiểu cách NuxtLink hoạt động
- Chạy development server

### Yêu cầu

1. **Tạo page chào mừng:**
   ```
   URL: /
   Nội dung:
   - Tiêu đề: "Xin chào! Tôi là [Tên của bạn]"
   - Một đoạn giới thiệu ngắn về bản thân
   - Nút "Tìm hiểu thêm" link đến /about
   ```

2. **Tạo page giới thiệu:**
   ```
   URL: /about
   Nội dung:
   - Tiêu đề: "Về tôi"
   - Danh sách sở thích (ít nhất 3)
   - Link quay về trang chủ
   ```

3. **Tạo layout với header chứa:**
   - Logo "My Site"
   - Navigation: Home, About

### Kết quả mong đợi

```
Trang Home (/)
┌─────────────────────────────────────┐
│ My Site      Home  About           │
│─────────────────────────────────────│
│                                     │
│   Xin chào! Tôi là Nam             │
│                                     │
│   Tôi đang học Nuxt 4             │
│                                     │
│   [Tìm hiểu thêm]                  │
│                                     │
└─────────────────────────────────────┘

Trang About (/about)
┌─────────────────────────────────────┐
│ My Site      Home  About           │
│─────────────────────────────────────│
│                                     │
│   Về tôi                           │
│                                     │
│   Sở thích:                        │
│   • Đọc sách                      │
│   • Lập trình                      │
│   • Nghe nhạc                      │
│                                     │
│   [← Quay về trang chủ]            │
│                                     │
└─────────────────────────────────────┘
```

### Gợi ý

```vue
<!-- Layout: app/layouts/default.vue -->
<template>
  <div>
    <header>
      <NuxtLink to="/">My Site</NuxtLink>
      <nav>
        <NuxtLink to="/">Home</NuxtLink>
        <NuxtLink to="/about">About</NuxtLink>
      </nav>
    </header>
    <slot />
  </div>
</template>
```

---

## Bài 2: Counter App

### Mục tiêu
- Học cách dùng `ref()` để tạo reactive state
- Học cách dùng `computed()` để tính toán
- Học cách dùng `v-on` (@click)

### Yêu cầu

1. **Tạo page counter:**
   ```
   URL: /counter
   Hiển thị:
   - Số đếm hiện tại (bắt đầu từ 0)
   - Nút "Tăng (+1)" 
   - Nút "Giảm (-1)"
   - Nút "Reset"
   - Số đếm được định dạng (VD: "Count: 5")
   ```

2. **Bonus - Thêm computed:**
   - Hiển thị "Số chẵn" hoặc "Số lẻ" dựa trên count
   - Hiển thị "Bình phương" của count

### Kết quả mong đợi

```
┌─────────────────────────────────────┐
│   Counter App                       │
│─────────────────────────────────────│
│                                     │
│   Count: 5                         │
│   (Số lẻ)                          │
│   Bình phương: 25                  │
│                                     │
│   [   -1   ]  [Reset]  [  +1   ]  │
│                                     │
└─────────────────────────────────────┘
```

### Gợi ý

```vue
<script setup>
// State
const count = ref(0)

// Computed
const isOdd = computed(() => count.value % 2 === 1)
const squared = computed(() => count.value * count.value)

// Methods
function increment() {
  count.value++
}
</script>
```

---

## Bài 3: Todo List Cơ Bản

### Mục tiêu
- Học cách dùng `v-for` để render danh sách
- Học cách dùng `v-model` cho input
- Học cách thêm/xóa items trong array

### Yêu cầu

1. **Tạo page todo list:**
   ```
   URL: /todo
   Tính năng:
   - Input để thêm todo mới
   - Nút "Thêm" để submit
   - Danh sách todos
   - Checkbox để toggle hoàn thành
   - Button để xóa todo
   ```

2. **Logic:**
   - Thêm todo: Input → Nhấn Enter hoặc click "Thêm"
   - Toggle: Click checkbox để đánh dấu hoàn thành
   - Xóa: Click button để xóa todo

### Kết quả mong đợi

```
┌─────────────────────────────────────┐
│   Todo List                        │
│─────────────────────────────────────│
│                                     │
│   [Nhập việc cần làm...    ]       │
│   [Thêm]                           │
│                                     │
│   ☐ Học Nuxt          [Xóa]       │
│   ☑ Làm bài tập       [Xóa]       │
│   ☐ Đọc tài liệu      [Xóa]       │
│                                     │
│   Đã hoàn thành: 1/3               │
│                                     │
└─────────────────────────────────────┘
```

### Gợi ý

```vue
<script setup>
// State
const newTodo = ref('')
const todos = ref([
  { id: 1, text: 'Học Nuxt', done: false },
  { id: 2, text: 'Làm bài tập', done: true }
])

// Methods
function addTodo() {
  if (!newTodo.value.trim()) return
  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    done: false
  })
  newTodo.value = ''
}

function removeTodo(id) {
  todos.value = todos.value.filter(t => t.id !== id)
}
</script>
```

---

## Bài 4: Profile Card

### Mục tiêu
- Học cách tạo reusable component
- Học cách truyền props
- Học cách dùng `<slot>`

### Yêu cầu

1. **Tạo ProfileCard component:**
   ```
   Props:
   - name (string, required)
   - role (string, default: "Developer")
   - avatar (string, optional - dùng placeholder nếu không có)
   - bio (string, optional)
   ```

2. **Sử dụng component trong page:**
   ```
   URL: /team
   Hiển thị 3 profile cards:
   - Nam - Frontend Developer
   - Minh - Backend Developer  
   - Linh - UI/UX Designer
   ```

### Kết quả mong đợi

```
┌─────────────────────────────────────┐
│   Our Team                          │
│─────────────────────────────────────│
│                                     │
│   ┌───────────┐ ┌───────────┐      │
│   │    📷     │ │    📷     │      │
│   │   Nam     │ │   Minh     │      │
│   │ Frontend  │ │ Backend   │      │
│   │ Developer │ │ Developer │      │
│   │           │ │           │      │
│   └───────────┘ └───────────┘      │
│                                     │
│   ┌───────────┐                     │
│   │    📷     │                     │
│   │   Linh    │                     │
│   │ UI/UX     │                     │
│   │ Designer  │                     │
│   └───────────┘                     │
│                                     │
└─────────────────────────────────────┘
```

### Gợi ý

```vue
<!-- app/components/ProfileCard.vue -->
<script setup>
defineProps({
  name: { type: String, required: true },
  role: { type: String, default: 'Developer' },
  avatar: { type: String, default: '' },
  bio: { type: String, default: '' }
})
</script>

<template>
  <div class="profile-card">
    <img :src="avatar || '/placeholder.png'" :alt="name">
    <h3>{{ name }}</h3>
    <p class="role">{{ role }}</p>
    <p v-if="bio" class="bio">{{ bio }}</p>
    <slot />
  </div>
</template>
```

---

## Bài 5: Navigation

### Mục tiêu
- Học cách tạo navigation component
- Hiểu active state của NuxtLink
- Học cách truyền data giữa pages

### Yêu cầu

1. **Tạo Navigation component với:**
   - Logo clickable về home
   - Menu items: Home, Blog, About, Contact
   - Active state styling (khi route active)
   - Responsive mobile menu (hamburger icon)

2. **Tạo các pages:**
   ```
   /         - Trang chủ
   /blog     - Trang blog (danh sách bài viết)
   /about    - Trang about
   /contact  - Trang contact (form đơn giản)
   ```

3. **Dùng navigation trong layout:**
   - Tạo layouts/default.vue với Navigation

### Kết quả mong đợi

```
┌─────────────────────────────────────┐
│ ☰ Logo      Home Blog About Contact│
│─────────────────────────────────────│
│                                     │
│   Nội dung page thay đổi theo URL   │
│                                     │
│   /         → "Chào mừng đến..."  │
│   /blog     → "Danh sách bài viết"  │
│   /about    → "Về chúng tôi"       │
│   /contact  → "Form liên hệ"       │
│                                     │
└─────────────────────────────────────┘

Active state:
[Khi ở /blog]
Logo      Home [Blog★] About Contact
                         ↑ Active styling
```

### Gợi ý

```vue
<!-- Navigation Component -->
<script setup>
const isMenuOpen = ref(false)

const menuItems = [
  { label: 'Home', to: '/' },
  { label: 'Blog', to: '/blog' },
  { label: 'About', to: '/about' },
  { label: 'Contact', to: '/contact' }
]

function toggleMenu() {
  isMenuOpen.value = !isMenuOpen.value
}
</script>

<template>
  <nav class="nav">
    <NuxtLink to="/" class="logo">Logo</NuxtLink>

    <button class="menu-btn" @click="toggleMenu">☰</button>

    <ul :class="{ 'menu-open': isMenuOpen }">
      <li v-for="item in menuItems" :key="item.to">
        <NuxtLink :to="item.to">{{ item.label }}</NuxtLink>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.nav {
  display: flex;
  justify-content: space-between;
}

.nav ul {
  display: flex;
  gap: 1rem;
}

/* Active state - NuxtLink tự thêm class này */
.nav a.router-link-active {
  color: #42b883;
  font-weight: bold;
}
</style>
```

---

## 🎯 Tổng Kết Bài Tập

```
BÀI TẬP              | ĐÃ HỌC ĐƯỢC
─────────────────────────────────────────
Bài 1: Hello World   | Tạo page, NuxtLink, layout
Bài 2: Counter       | ref(), computed(), @click
Bài 3: Todo List    | v-for, v-model, array methods
Bài 4: Profile Card  | Props, slots, components
Bài 5: Navigation   | Active state, responsive menu

SAU 5 BÀI NÀY, BẠN ĐÃ:
✅ Hiểu cách Nuxt tạo routes
✅ Biết dùng reactive state
✅ Biết tạo components
✅ Biết dùng v-for, v-model
✅ Biết tạo layout
```

---

## ▶️ Tiếp Theo

Hoàn thành 5 bài tập trên?
→ [Intermediate Exercises](../02-intermediate/) - Bài tập trung bình

hoặc → [Core Concepts](../02-core-concepts/) - Học kiến thức nâng cao hơn

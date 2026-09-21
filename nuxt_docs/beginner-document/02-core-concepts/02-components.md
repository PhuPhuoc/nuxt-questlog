# Components & Props

> **Mục tiêu:** Học cách tạo và sử dụng Vue components trong Nuxt.

## Mục lục

1. [Components là gì?](#1-components-là-gì)
2. [Tạo Component](#2-tạo-component)
3. [Props - Truyền data](#3-props---truyền-data)
4. [Emits - Nhận events](#4-emits---nhận-events)
5. [Slots - Nội dung](#5-slots---nội-dung)
6. [Lazy Components](#6-lazy-components)

---

## 1. Components Là Gì?

### Khái niệm đơn giản

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPONENTS LÀ GÌ?                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Component = Một "khối" code có thể tái sử dụng                   │
│                                                                     │
│  Ví dụ:                                                            │
│  ├── Header      → Logo, navigation                           │
│  ├── Button      → Nút bấm với variants                       │
│  ├── Card        → Container cho nội dung                       │
│  ├── Modal       → Popup dialog                               │
│  └── UserProfile → Hiển thị thông tin user                    │
│                                                                     │
│  LỢI ÍCH:                                                         │
│  ├── Tái sử dụng code                                           │
│  ├── Dễ bảo trì                                                 │
│  ├── Tổ chức code tốt                                          │
│  └── Làm việc nhóm dễ dàng                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh có/không có Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHÔNG CÓ COMPONENTS                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  pages/index.vue                                                  │
│  ────────────────────────                                          │
│  <div>                                                            │
│    <div class="card">                                              │
│      <h3>Product 1</h3>                                          │
│      <p>$100</p>                                                  │
│      <button>Buy</button>                                         │
│    </div>                                                         │
│    <div class="card">                                              │
│      <h3>Product 2</h3>                                          │
│      <p>$200</p>                                                  │
│      <button>Buy</button>                                         │
│    </div>                                                         │
│    ... (lặp lại nhiều lần)                                       │
│  </div>                                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                    CÓ COMPONENTS                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  pages/index.vue                                                  │
│  ────────────────────────                                          │
│  <ProductCard product={...} />                                    │
│  <ProductCard product={...} />                                    │
│  <ProductCard product={...} />                                    │
│                                                                     │
│  components/ProductCard.vue                                       │
│  ─────────────────────────────────                                 │
│  <div class="card">                                                │
│    <h3>{{ product.name }}</h3>                                    │
│    <p>{{ product.price }}</p>                                     │
│    <button>Buy</button>                                          │
│  </div>                                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Tạo Component

### 2.1 Cấu trúc thư mục

```
app/
├── pages/
│   └── index.vue
└── components/
    ├── Header.vue          → <Header />
    ├── Footer.vue          → <Footer />
    ├── ProductCard.vue     → <ProductCard />
    └── 📁 ui/
        ├── Button.vue     → <UiButton />
        └── Modal.vue      → <UiModal />
```

### 2.2 Component đơn giản

```vue
<!-- app/components/Greeting.vue -->
<script setup>
// Component này hiển thị lời chào
defineProps({
  name: {
    type: String,
    default: 'Khách'
  }
})
</script>

<template>
  <div class="greeting">
    <h2>Chào {{ name }}!</h2>
    <slot></slot>
  </div>
</template>

<style scoped>
.greeting {
  padding: 1rem;
  background: #f5f5f5;
  border-radius: 8px;
}
</style>
```

### 2.3 Sử dụng Component

```vue
<!-- pages/index.vue -->
<script setup>
const userName = 'Nam'
</script>

<template>
  <div>
    <!-- Sử dụng component - KHÔNG cần import! -->
    <Greeting name="Nam">
      <p>Chào mừng bạn đến với website!</p>
    </Greeting>

    <!-- Với biến -->
    <Greeting :name="userName" />
  </div>
</template>
```

---

## 3. Props - Truyền Data

### 3.1 Props là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PROPS - TRUYỀN DATA TỪ PARENT → CHILD               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Parent Component:                                                 │
│  ─────────────────                                                 │
│  <UserCard name="Nam" email="nam@example.com" />                  │
│          │                                                         │
│          │ props: { name, email }                                 │
│          ↓                                                         │
│  Child Component:                                                  │
│  ─────────────────                                                 │
│  defineProps({ name, email })                                    │
│  → Có thể dùng {{ name }}, {{ email }} trong template          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Định nghĩa Props

```vue
<!-- Button.vue -->
<script setup>
// Cách 1: TypeScript (Khuyến nghị)
const props = defineProps<{
  label: string
  variant?: 'primary' | 'secondary' | 'outline'
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
}>()

// Default values với withDefaults
withDefaults(defineProps<{
  label: string
  variant?: 'primary' | 'secondary' | 'outline'
  size?: 'small' | 'medium' | 'large'
}>(), {
  variant: 'primary',
  size: 'medium'
})
</script>
```

```vue
<!-- Button.vue - Cách 2: Options API style -->
<script setup>
defineProps({
  label: {
    type: String,
    required: true
  },
  variant: {
    type: String,
    default: 'primary',
    validator: (v) => ['primary', 'secondary', 'outline'].includes(v)
  },
  size: {
    type: String,
    default: 'medium'
  }
})
</script>
```

### 3.3 Sử dụng Props

```vue
<!-- Parent Component -->
<template>
  <div>
    <!-- Basic -->
    <Button label="Click me" />

    <!-- Dynamic -->
    <Button :label="buttonText" />

    <!-- Variants -->
    <Button label="Primary" variant="primary" />
    <Button label="Secondary" variant="secondary" />
    <Button label="Outline" variant="outline" />

    <!-- Sizes -->
    <Button label="Small" size="small" />
    <Button label="Medium" size="medium" />
    <Button label="Large" size="large" />
  </div>
</template>

<script setup>
const buttonText = ref('Dynamic Button')
</script>
```

### 3.4 Props với Objects

```vue
<!-- UserCard.vue -->
<script setup>
defineProps<{
  user: {
    id: number
    name: string
    email: string
    avatar?: string
    role?: 'admin' | 'user' | 'guest'
  }
}>()
</script>

<template>
  <div class="user-card">
    <img :src="user.avatar || '/default-avatar.png'" :alt="user.name" />
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
    <span v-if="user.role" class="badge">{{ user.role }}</span>
  </div>
</template>
```

```vue
<!-- Parent -->
<script setup>
const user = ref({
  id: 1,
  name: 'Nam Nguyen',
  email: 'nam@example.com',
  role: 'admin'
})
</script>

<template>
  <UserCard :user="user" />
</template>
```

---

## 4. Emits - Nhận Events

### 4.1 Emits là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EMITS - GỦI EVENT TỪ CHILD → PARENT                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Child Component:                                                  │
│  ─────────────────                                                 │
│  const emit = defineEmits(['click', 'submit'])                   │
│  emit('click', data)  → Gửi event 'click' với data              │
│          │                                                         │
│          ↓                                                         │
│  Parent Component:                                                 │
│  ─────────────────                                                 │
│  <ChildComponent @click="handleClick" />                          │
│          │                                                         │
│          ↓                                                         │
│  function handleClick(data) { ... }  → Nhận được data            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 Định nghĩa Emits

```vue
<!-- Alert.vue -->
<script setup>
// Định nghĩa emits
const emit = defineEmits<{
  close: []                    // Không có payload
  submit: [data: object]     // Có payload là object
  custom: [name: string, value: number]  // Nhiều payloads
}>()

// Gửi event
function handleClose() {
  emit('close')
}

function handleSubmit() {
  emit('submit', { success: true, message: 'Done!' })
}
</script>

<template>
  <div class="alert">
    <slot></slot>
    <button @click="handleClose">Close</button>
  </div>
</template>
```

### 4.3 Lắng nghe Emits

```vue
<!-- Parent Component -->
<template>
  <div>
    <Alert @close="onClose">
      <p>Alert content here!</p>
    </Alert>

    <Alert @submit="onSubmit">
      <p>Form data:</p>
    </Alert>
  </div>
</template>

<script setup>
function onClose() {
  console.log('Alert closed!')
}

function onSubmit(data) {
  console.log('Submit:', data)
}
</script>
```

### 4.4 Emits với Validation

```vue
<!-- Input.vue -->
<script setup>
const emit = defineEmits<{
  'update:modelValue': [value: string]
  focus: [event: FocusEvent]
  blur: [event: FocusEvent]
}>()

function handleInput(event: Event) {
  const target = event.target as HTMLInputElement
  emit('update:modelValue', target.value)
}
</script>

<template>
  <input @input="handleInput" @focus="emit('focus', $event)" />
</template>
```

---

## 5. Slots - Nội Dung

### 5.1 Slots là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SLOTS - TRUYỀN NỘI DUNG                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  <Layout>                                                         │
│    <Header />      ← Truyền Header vào Layout                  │
│    <Main />        ← Truyền Main vào Layout                    │
│    <Footer />      ← Truyền Footer vào Layout                  │
│  </Layout>                                                       │
│                                                                     │
│  Layout Component:                                                │
│  ─────────────────                                                │
│  <div class="layout">                                             │
│    <slot name="header" />    → Hiển thị Header ở đây           │
│    <slot name="main" />      → Hiển thị Main ở đây             │
│    <slot name="footer" />    → Hiển thị Footer ở đây           │
│  </div>                                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Default Slot

```vue
<!-- Card.vue -->
<script setup>
defineProps({
  title: String
})
</script>

<template>
  <div class="card">
    <h3>{{ title }}</h3>
    <slot></slot>  <!-- Default slot - nội dung không có name -->
  </div>
</template>
```

```vue
<!-- Parent -->
<Card title="My Card">
  <p>Content goes here!</p>
  <button>Click me</button>
</Card>
```

### 5.3 Named Slots

```vue
<!-- BaseLayout.vue -->
<template>
  <div class="layout">
    <header>
      <slot name="header" />
    </header>

    <main>
      <slot name="main" />
    </main>

    <footer>
      <slot name="footer" />
    </footer>
  </div>
</template>
```

```vue
<!-- Parent -->
<BaseLayout>
  <template #header>
    <Logo />
    <Navigation />
  </template>

  <template #main>
    <Article />
  </template>

  <template #footer>
    <p>© 2024 My Site</p>
  </template>
</BaseLayout>
```

### 5.4 Scoped Slots

```vue
<!-- DataList.vue -->
<script setup>
defineProps<{
  items: string[]
}>()
</script>

<template>
  <ul>
    <!-- Slot với props - parent có thể nhận data từ child -->
    <slot
      v-for="item in items"
      :item="item"
      :index="items.indexOf(item)"
    />
  </ul>
</template>
```

```vue
<!-- Parent - Nhận props từ slot -->
<DataList :items="['A', 'B', 'C']">
  <template #default="{ item, index }">
    <li>{{ index + 1 }}. {{ item }}</li>
  </template>
</DataList>
```

---

## 6. Lazy Components

### 6.1 Lazy loading là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAZY LOADING                                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NON-LAZY:                                                        │
│  ├── Tải ngay khi page load                                    │
│  ├── Fast initial render                                          │
│  └── Nếu component nặng → page chậm                              │
│                                                                     │
│  LAZY:                                                            │
│  ├── Chỉ tải khi CẦN                                          │
│  ├── Page load nhanh                                             │
│  └── Phù hợp cho: Modal, Charts, Heavy components               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.2 Sử dụng Lazy Components

```vue
<template>
  <div>
    <!-- Tải ngay (non-lazy) -->
    <Header />
    <Sidebar />

    <!-- Tải khi cần (lazy) -->
    <LazyModal v-if="showModal" @close="showModal = false" />
    <LazyHeavyChart v-if="showChart" />
    <LazyDataTable :data="tableData" />

    <!-- Tải khi hiện trong viewport -->
    <LazyOnVisibleComponent />
  </div>
</template>
```

### 6.3 Dynamic Import

```vue
<script setup>
// Cách khác: Dynamic import trong script
const HeavyComponent = defineAsyncComponent(() =>
  import('./HeavyComponent.vue')
)
</script>

<template>
  <div>
    <HeavyComponent v-if="show" />
  </div>
</template>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPONENTS CHEAT SHEET                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TẠO COMPONENT:                                                  │
│  ├── File: app/components/Button.vue                              │
│  ├── Name: PascalCase                                             │
│  └── Auto-import: <Button />                                       │
│                                                                     │
│  PROPS:                                                           │
│  ├── defineProps<{ name: string }>()                             │
│  ├── withDefaults(defineProps<{...}>(), {})                        │
│  └── Dùng: :prop-name="value"                                    │
│                                                                     │
│  EMITS:                                                           │
│  ├── defineEmits<{ event: [payload] }>()                         │
│  └── Lắng nghe: @event="handler"                                │
│                                                                     │
│  SLOTS:                                                           │
│  ├── Default: <slot />                                            │
│  ├── Named: <slot name="header" />                               │
│  └── Scoped: <slot :data="data" />                              │
│                                                                     │
│  LAZY:                                                            │
│  └── Prefix: <LazyComponentName />                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [03-routing-basics.md](03-routing-basics.md) - Routing cơ bản

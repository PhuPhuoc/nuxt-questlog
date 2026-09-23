# Components & Props

> **Mục tiêu:** Học cách tạo và sử dụng Vue components trong Nuxt 4 với TypeScript.

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
│                    COMPONENTS LÀ GÌ?                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Component = Một "khối" code có thể tái sử dụng                     │
│                                                                     │
│  Ví dụ:                                                             │
│  ├── Header      → Logo, navigation                                 │
│  ├── Button      → Nút bấm với variants                             │
│  ├── Card        → Container cho nội dung                           │
│  ├── Modal       → Popup dialog                                     │
│  └── UserProfile → Hiển thị thông tin user                          │
│                                                                     │
│  LỢI ÍCH:                                                           │
│  ├── Tái sử dụng code                                               │
│  ├── Dễ bảo trì                                                     │
│  ├── Tổ chức code tốt                                               │
│  └── Làm việc nhóm dễ dàng                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh có/không có Components

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHÔNG CÓ COMPONENTS                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  app/pages/index.vue                                                 │
│  ────────────────────────                                           │
│  <div>                                                              │
│    <div class="card">                                               │
│      <h3>Product 1</h3>                                             │
│      <p>$100</p>                                                    │
│      <button>Buy</button>                                            │
│    </div>                                                           │
│    <div class="card">                                               │
│      <h3>Product 2</h3>                                             │
│      <p>$200</p>                                                    │
│      <button>Buy</button>                                            │
│    </div>                                                           │
│    ... (lặp lại nhiều lần)                                          │
│  </div>                                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                    CÓ COMPONENTS                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  app/pages/index.vue                                                 │
│  ────────────────────────                                           │
│  <ProductCard product={...} />                                      │
│  <ProductCard product={...} />                                      │
│  <ProductCard product={...} />                                      │
│                                                                     │
│  app/components/ProductCard.vue                                     │
│  ─────────────────────────────────────                             │
│  <div class="card">                                                 │
│    <h3>{{ product.name }}</h3>                                      │
│    <p>{{ product.price }}</p>                                       │
│    <button>Buy</button>                                             │
│  </div>                                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Tạo Component

### 2.1 Cấu trúc thư mục

```
app/
├── components/
│   ├── Header.vue          → <Header />
│   ├── Footer.vue          → <Footer />
│   ├── ProductCard.vue     → <ProductCard />
│   └── 📁 ui/
│       ├── Button.vue     → <UiButton />
│       └── Modal.vue      → <UiModal />
├── pages/
│   └── index.vue
```

### 2.2 Component đơn giản

```vue
<!-- app/components/Greeting.vue -->
<script setup lang="ts">
// Component này hiển thị lời chào
defineProps<{
  name: string
}>()
</script>

<template>
  <div class="greeting">
    <h2>Chào {{ name }}!</h2>
    <slot />
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
<!-- app/pages/index.vue -->
<script setup lang="ts">
const userName = ref('Nam')
</script>

<template>
  <div>
    <!-- Sử dụng component - KHÔNG cần import! -->
    <Greeting :name="userName">
      <p>Chào mừng bạn đến với website!</p>
    </Greeting>
  </div>
</template>
```

**Trong Nuxt 4:**
- Component trong `app/components/` được **tự động import** (auto-import)
- Không cần `import` thủ công
- Tên component tự động PascalCase

---

## 3. Props - Truyền Data

### 3.1 Props là gì? Dùng để làm gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PROPS - TRUYỀN DATA TỪ PARENT → CHILD            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Parent Component:                                                  │
│  ─────────────────                                                  │
│  <UserCard name="Nam" email="nam@example.com" />                    │
│          │                                                          │
│          │ props: { name, email }                                   │
│          ↓                                                          │
│  Child Component:                                                   │
│  ─────────────────                                                  │
│  defineProps<{ name, email }>()                                    │
│  → Có thể dùng {{ name }}, {{ email }} trong template              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Props dùng để:** Truyền data từ Parent xuống Child để hiển thị hoặc config behavior.

### 3.2 Props Có Sẵn Hay Cần Custom?

**Props là tính năng CÓ SẴN của Vue 3/Nuxt 4.** Không cần cài thêm gì.

- Khai báo bằng `defineProps<{...}>()` trong `<script setup>`
- Tự động TypeScript inference

### 3.3 Cơ Chế Hoạt Động - One-Way Data Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LIFECYCLE CỦA PROPS                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. PARENT RENDER                                                   │
│     Parent state thay đổi → re-render                               │
│                                                                     │
│  2. PROPS UPDATE                                                    │
│     Vue nhận thấy props mới → đẩy xuống Child                      │
│     ⚠️ Props là ONE-WAY DATA FLOW!                                  │
│                                                                     │
│  3. CHILD RENDER                                                    │
│     Child nhận props → re-render với data mới                      │
│                                                                     │
│  4. EMIT EVENT (nếu cần)                                           │
│     Child muốn thay đổi → gửi event lên Parent                     │
│     Parent xử lý → cập nhật state → quay lại bước 1               │
│                                                                     │
│  ─────────────────────────────────────────                          │
│  IMPORTANT: Child KHÔNG thể trực tiếp sửa props!                   │
│  Nếu sửa → lỗi "Props are readonly!"                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Tại sao Props là One-Way?**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VÌ SAO ONE-WAY DATA FLOW?                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ❌ MULTI-WAY (2 chiều):                                            │
│     A → B → C → A (vòng lặp) → RỐI, khó debug                     │
│                                                                     │
│  ✅ ONE-WAY:                                                        │
│     Parent (nguồn thật) → Child (tiếp nhận)                         │
│     Dễ hiểu: luôn biết data từ đâu ra                              │
│                                                                     │
│  Khi nào cần thay đổi?                                             │
│  → Child emit event → Parent xử lý → Parent cập nhật               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.4 Khai Báo Props - TypeScript

```ts
// app/components/Button.vue
<script setup lang="ts">
// Cách 1: TypeScript generic (Khuyến nghị)
const props = defineProps<{
  label: string
  variant?: 'primary' | 'secondary' | 'outline'
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
}>()

// Props là reactive trong template
console.log(props.label)
</script>

<template>
  <button
    :class="['btn', `btn-${props.variant}`, `btn-${props.size}`]"
    :disabled="props.disabled"
  >
    {{ props.label }}
  </button>
</template>
```

```ts
// app/components/Button.vue
<script setup lang="ts">
// Cách 2: Default values với withDefaults
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

### 3.5 Props Validation với TypeScript

```ts
// app/components/ProductCard.vue
<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
  image?: string
  category?: 'electronics' | 'clothing' | 'food'
}

const props = defineProps<{
  product: Product
  showActions?: boolean
}>()

// TypeScript tự validate kiểu dữ liệu
// Nếu truyền sai type → TypeScript báo lỗi ngay
</script>
```

### 3.6 Props với Reactive Data

```vue
<!-- Parent - data thay đổi → props tự động update -->
<script setup lang="ts">
const userName = ref('Nam')

// Sau 2 giây, userName thay đổi
// → Props trong Child cũng tự động thay đổi
setTimeout(() => {
  userName.value = 'Minh'
}, 2000)
</script>

<template>
  <!-- Props truyền xuống Child là REACTIVE! -->
  <Greeting :name="userName" />
</template>
```

### 3.7 Props và Computed

```vue
<!-- app/components/ProductCard.vue -->
<script setup lang="ts">
const props = defineProps<{
  price: number
  discount?: number
}>()

// Computed tự recalculate khi props thay đổi
const finalPrice = computed(() => {
  if (props.discount) {
    return props.price * (1 - props.discount / 100)
  }
  return props.price
})

const formattedPrice = computed(() => {
  return new Intl.NumberFormat('vi-VN', {
    style: 'currency',
    currency: 'VND'
  }).format(props.finalPrice)
})
</script>

<template>
  <div class="product-card">
    <p>Giá gốc: {{ formattedPrice }}</p>
    <p v-if="props.discount">Giảm {{ props.discount }}%</p>
  </div>
</template>
```

### 3.8 Props với Objects/Arrays

```vue
<!-- app/components/UserCard.vue -->
<script setup lang="ts">
interface User {
  id: number
  name: string
  email: string
  avatar?: string
  role: 'admin' | 'user' | 'guest'
}

defineProps<{
  user: User
  size?: 'sm' | 'md' | 'lg'
}>()
</script>

<template>
  <div class="user-card">
    <img
      :src="user.avatar || '/default-avatar.png'"
      :alt="user.name"
      class="avatar"
    />
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
    <span class="badge">{{ user.role }}</span>
  </div>
</template>
```

```vue
<!-- app/pages/index.vue -->
<script setup lang="ts">
const user = ref<User>({
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

### 3.9 Props Cases Thực Tế Hay Dùng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PROPS - CASE THỰC TẾ                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CASE 1: CONFIGURATION COMPONENT                                    │
│  ───────────────────────────────────────                            │
│  <Button variant="primary" size="large" disabled />                  │
│  Component nhận config → render theo config                         │
│                                                                     │
│  CASE 2: DATA DISPLAY                                              │
│  ───────────────────────────────────────                            │
│  <UserCard :user="currentUser" />                                   │
│  <DataTable :rows="tableData" :columns="columns" />                  │
│  Truyền data xuống để hiển thị                                     │
│                                                                     │
│  CASE 3: CALLBACK/HANDLER                                          │
│  ───────────────────────────────────────                            │
│  <ConfirmButton :on-confirm="handleDelete" />                        │
│  Truyền function như một prop!                                      │
│                                                                     │
│  CASE 4: CONTROL BEHAVIOR                                          │
│  ───────────────────────────────────────                            │
│  <Modal :is-open="show" @close="show = false" />                    │
│  Control behavior từ bên ngoài                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Case 3 - Callback Props:**

```vue
<!-- app/components/ConfirmButton.vue -->
<script setup lang="ts">
const props = defineProps<{
  onConfirm: () => Promise<void>
  confirmText?: string
  variant?: 'danger' | 'primary'
}>()

const isLoading = ref(false)

async function handleClick() {
  isLoading.value = true
  try {
    await props.onConfirm()
  } finally {
    isLoading.value = false
  }
}
</script>

<template>
  <button
    :class="['btn', `btn-${props.variant}`]"
    :disabled="isLoading"
    @click="handleClick"
  >
    {{ isLoading ? 'Loading...' : (props.confirmText || 'Confirm') }}
  </button>
</template>
```

```vue
<!-- app/pages/index.vue -->
<script setup lang="ts">
async function handleDelete() {
  await $fetch(`/api/items/${itemId}`, { method: 'DELETE' })
  await refreshNuxtData()
}

async function handleArchive() {
  await $fetch(`/api/items/${itemId}/archive`, { method: 'POST' })
}
</script>

<template>
  <!-- Cùng component, behavior khác nhau! -->
  <ConfirmButton
    :on-confirm="handleDelete"
    confirm-text="Xóa"
    variant="danger"
  />
  <ConfirmButton
    :on-confirm="handleArchive"
    confirm-text="Lưu trữ"
    variant="primary"
  />
</template>
```

---

## 4. Emits - Nhận Events

### 4.1 Emits là gì? Dùng để làm gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EMITS - GỦI EVENT TỪ CHILD → PARENT              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Child Component:                                                   │
│  ─────────────────                                                  │
│  const emit = defineEmits<{ click: []; submit: [data: object] }>() │
│  emit('click', data) → Gửi event 'click' với data                  │
│          │                                                          │
│          ↓                                                          │
│  Parent Component:                                                  │
│  ─────────────────                                                  │
│  <ChildComponent @click="handleClick" />                            │
│          │                                                          │
│          ↓                                                          │
│  function handleClick(data) { ... } → Nhận được data               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Emits dùng để:** Child gửi event + data lên Parent khi có action xảy ra (click, submit, etc.).

### 4.2 Emits Có Sẵn Hay Cần Custom?

**Emits là tính năng CÓ SẴN của Vue 3/Nuxt 4.** Không cần cài thêm gì.

- Khai báo bằng `defineEmits<{...}>()` trong `<script setup>`
- TypeScript inference tự động

### 4.3 Cơ Chế Hoạt Động - Emits Lifecycle

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EMITS LIFECYCLE - TỪNG BƯỚC                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. USER TƯƠNG TÁC                                                  │
│     User click button, submit form, hover...                        │
│                                                                     │
│  2. CHILD XỬ LÝ                                                     │
│     function handleClick() {                                        │
│       // Validate data (optional)                                   │
│       // Transform data (optional)                                  │
│       emit('submit', payload)                                       │
│     }                                                               │
│                                                                     │
│  3. EMIT TRIGGERED                                                  │
│     emit('submit', { id: 1, name: 'test' })                         │
│                                                                     │
│  4. PARENT NHẬN EVENT                                               │
│     <Child @submit="handleParentSubmit" />                          │
│     function handleParentSubmit(payload) { ... }                    │
│                                                                     │
│  5. PARENT UPDATE STATE                                             │
│     Parent state thay đổi → re-render                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.4 Tại Sao Cần Emits?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    VÌ SAO DÙNG EMITS?                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ❌ Child trực tiếp sửa Parent state?                              │
│     → KHÔNG ĐƯỢC! Props are read-only                               │
│                                                                     │
│  ✅ Giải pháp: CHILD EMIT → PARENT XỬ LÝ                           │
│                                                                     │
│     ┌─────────┐      emit('event')       ┌─────────┐                │
│     │  Child  │  ───────────────────────→│  Parent │                │
│     │         │                          │         │                │
│     │ - Props │                          │ - State │                │
│     │ - Logic │                          │ - Logic │                │
│     │ - Emit  │                          │         │                │
│     └─────────┘                          └─────────┘                │
│                                                                     │
│  Child giữ logic riêng, nhưng STATE vẫn ở Parent!                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.5 Khai Báo Emits - TypeScript

```ts
// app/components/Alert.vue
<script setup lang="ts">
const emit = defineEmits<{
  // Event không có payload
  close: []
  // Event có payload
  submit: [data: { id: number; name: string }]
  // Event với nhiều params
  update: [field: string, value: unknown]
}>()

function handleClose() {
  emit('close')
}

function handleSubmit() {
  emit('submit', { id: 1, name: 'test' })
}
</script>

<template>
  <div class="alert">
    <slot />
    <button @click="handleClose">Close</button>
    <button @click="handleSubmit">Submit</button>
  </div>
</template>
```

### 4.6 Lắng Nghe Emits - Cách Dùng

```vue
<!-- app/pages/index.vue -->
<script setup lang="ts">
function onClose() {
  console.log('Alert closed!')
}

function onSubmit(data: { id: number; name: string }) {
  console.log('Submit:', data)
}
</script>

<template>
  <div>
    <Alert @close="onClose" @submit="onSubmit">
      <p>Alert content here!</p>
    </Alert>
  </div>
</template>
```

### 4.7 v-model và Emits

```
┌─────────────────────────────────────────────────────────────────────┐
│                    V-MODEL HOẠT ĐỘNG NHƯ THẾ NÀO?                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  <CustomInput v-model="email" />                                    │
│                                                                     │
│  Tương đương với:                                                   │
│  <CustomInput                                                       │
│    :modelValue="email"                                              │
│    @update:modelValue="email = $event"                              │
│  />                                                                 │
│                                                                     │
│  Khi input thay đổi:                                                │
│  Child emit('update:modelValue', newValue)                          │
│  → Parent nhận → cập nhật `email` → props truyền xuống lại          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```vue
<!-- app/components/CustomInput.vue -->
<script setup lang="ts">
const props = defineProps<{
  modelValue: string
  placeholder?: string
}>()

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
  <input
    :value="props.modelValue"
    :placeholder="props.placeholder"
    @input="handleInput"
    @focus="emit('focus', $event)"
    @blur="emit('blur', $event)"
  />
</template>
```

**v-model với argument (nhiều giá trị):**

```vue
<!-- app/components/DateRangePicker.vue -->
<script setup lang="ts">
const props = defineProps<{
  startDate: string
  endDate: string
}>()

const emit = defineEmits<{
  'update:startDate': [value: string]
  'update:endDate': [value: string]
}>()
</script>

<template>
  <div class="date-range">
    <input
      :value="props.startDate"
      @input="emit('update:startDate', ($event.target as HTMLInputElement).value)"
    />
    <input
      :value="props.endDate"
      @input="emit('update:endDate', ($event.target as HTMLInputElement).value)"
    />
  </div>
</template>
```

```vue
<!-- app/pages/index.vue -->
<script setup lang="ts">
const dateRange = ref({
  start: '2024-01-01',
  end: '2024-12-31'
})
</script>

<template>
  <DateRangePicker
    v-model:startDate="dateRange.start"
    v-model:endDate="dateRange.end"
  />
</template>
```

### 4.8 Emits Cases Thực Tế Hay Dùng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EMITS - CASE THỰC TẾ                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CASE 1: FORM HANDLING                                              │
│  ───────────────────────────────────────                            │
│  <Form @submit="handleSubmit" @invalid="handleInvalid" />           │
│  Component tự validate, khi submit hợp lệ → emit lên                │
│                                                                     │
│  CASE 2: LIST ITEM ACTIONS                                          │
│  ───────────────────────────────────────                            │
│  <DataTable @row-click="handleRowClick" @delete="handleDelete" />   │
│  Item action → emit với id/data → Parent xử lý                      │
│                                                                     │
│  CASE 3: MODAL CONTROL                                              │
│  ───────────────────────────────────────                            │
│  <Modal @close="showModal = false" @confirm="handleConfirm" />      │
│  Control lifecycle từ bên ngoài                                     │
│                                                                     │
│  CASE 4: TOGGLE/SWITCH                                              │
│  ───────────────────────────────────────                            │
│  <ToggleSwitch @update:modelValue="handleToggle" :model-value="v" />│
│  Click → toggle → emit → Parent cập nhật state                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Case 1 - Form Component:**

```vue
<!-- app/components/LoginForm.vue -->
<script setup lang="ts">
interface FormData {
  email: string
  password: string
}

interface FormErrors {
  email?: string
  password?: string
}

const emit = defineEmits<{
  submit: [data: FormData]
  invalid: [errors: FormErrors]
}>()

const form = reactive({
  email: '',
  password: ''
})

function validate(): FormErrors {
  const errors: FormErrors = {}

  if (!form.email.includes('@')) {
    errors.email = 'Email không hợp lệ'
  }

  if (form.password.length < 6) {
    errors.password = 'Mật khẩu phải có ít nhất 6 ký tự'
  }

  return errors
}

function handleSubmit() {
  const errors = validate()

  if (Object.keys(errors).length === 0) {
    emit('submit', { ...form })
  } else {
    emit('invalid', errors)
  }
}
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <div class="form-group">
      <input v-model="form.email" type="email" placeholder="Email" />
      <span v-if="form.email">Error here</span>
    </div>

    <div class="form-group">
      <input v-model="form.password" type="password" placeholder="Password" />
    </div>

    <button type="submit">Submit</button>
  </form>
</template>
```

```vue
<!-- app/pages/login.vue -->
<script setup lang="ts">
async function handleSubmit(data: { email: string; password: string }) {
  await $fetch('/api/login', {
    method: 'POST',
    body: data
  })
  navigateTo('/dashboard')
}

function handleInvalid(errors: { email?: string; password?: string }) {
  console.log('Validation errors:', errors)
}
</script>

<template>
  <LoginForm @submit="handleSubmit" @invalid="handleInvalid" />
</template>
```

**Case 2 - List Item Actions:**

```vue
<!-- app/components/ProductList.vue -->
<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
}

defineProps<{
  products: Product[]
}>()

const emit = defineEmits<{
  'product-click': [product: Product]
  'product-delete': [productId: number]
  'product-edit': [product: Product]
}>()
</script>

<template>
  <div class="product-list">
    <div
      v-for="product in products"
      :key="product.id"
      class="product-item"
    >
      <h3 @click="emit('product-click', product)">{{ product.name }}</h3>
      <p>{{ product.price }}</p>
      <button @click="emit('product-edit', product)">Sửa</button>
      <button @click="emit('product-delete', product.id)">Xóa</button>
    </div>
  </div>
</template>
```

```vue
<!-- app/pages/products.vue -->
<script setup lang="ts">
const { data: products } = await useFetch('/api/products')

function handleView(product: Product) {
  navigateTo(`/products/${product.id}`)
}

async function handleDelete(id: number) {
  if (confirm('Xóa sản phẩm này?')) {
    await $fetch(`/api/products/${id}`, { method: 'DELETE' })
    await refreshNuxtData()
  }
}
</script>

<template>
  <ProductList
    :products="products || []"
    @product-click="handleView"
    @product-delete="handleDelete"
    @product-edit="openEditModal"
  />
</template>
```

---

## 5. Slots - Nội Dung

### 5.1 Slots là gì? Dùng để làm gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SLOTS - TRUYỀN NỘI DUNG                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  <Layout>                                                           │
│    <Header />      ← Truyền Header vào Layout                      │
│    <Main />        ← Truyền Main vào Layout                         │
│    <Footer />      ← Truyền Footer vào Layout                       │
│  </Layout>                                                          │
│                                                                     │
│  Layout Component:                                                  │
│  ─────────────────                                                  │
│  <div class="layout">                                              │
│    <slot name="header" />  → Hiển thị Header ở đây                 │
│    <slot name="main" />    → Hiển thị Main ở đây                  │
│    <slot name="footer" />  → Hiển thị Footer ở đây                │
│  </div>                                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Slots dùng để:** Truyền nội dung (template/HTML) từ Parent vào Child tại vị trí xác định.

### 5.2 Slots Có Sẵn Hay Cần Custom?

**Slots là tính năng CÓ SẴN của Vue 3/Nuxt 4.** Không cần cài thêm gì.

- Default slot: `<slot />`
- Named slot: `<slot name="header" />`
- Scoped slot: `<slot :data="data" />`

### 5.3 Cơ Chế Hoạt Động - Slots Behind The Scenes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SLOTS WORKFLOW                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. PARENT ĐỊNH NGHĨA CONTENT                                       │
│     <Card>                                                          │
│       <p>Nội dung bên trong</p>                                     │
│     </Card>                                                         │
│                                                                     │
│  2. NUXT/VUE WRAP CONTENT                                           │
│     Card nhận được content như một "slot"                           │
│     Content chưa render, chờ Child quyết định vị trí                │
│                                                                     │
│  3. CHILD CHỌN VỊ TRÍ                                               │
│     <div><slot /></div>                                             │
│     → Vue render content của Parent tại đây                         │
│                                                                     │
│  4. CONTENT RENDER                                                  │
│     Kết quả: <div><p>Nội dung bên trong</p></div>                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.4 Default Slot

```vue
<!-- app/components/Card.vue -->
<script setup lang="ts">
defineProps<{
  title: string
}>()
</script>

<template>
  <div class="card">
    <h3>{{ title }}</h3>
    <slot />  <!-- Default slot - nội dung không có name -->
  </div>
</template>
```

```vue
<!-- app/pages/index.vue -->
<Card title="My Card">
  <p>Content goes here!</p>
  <button>Click me</button>
</Card>
```

### 5.5 Named Slots

```vue
<!-- app/components/BaseLayout.vue -->
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
<!-- app/pages/index.vue -->
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

### 5.6 Scoped Slots - Child Gửi Data Lại Cho Parent

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SCOPED SLOTS                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ĐÂY LÀ ĐIỂM ĐẶC BIỆT CỦA SLOTS!                                    │
│                                                                     │
│  Bình thường: Parent → Child (truyền content)                       │
│  Scoped Slots: Parent ← Child (Child gửi data lại Parent)           │
│                                                                     │
│  Child có thể truyền biến qua slot:                                 │
│  <slot :item="item" :index="i" />                                   │
│                                                                     │
│  Parent nhận bằng destructuring:                                    │
│  <template #default="{ item, index }">                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```vue
<!-- app/components/DataList.vue -->
<script setup lang="ts">
defineProps<{
  items: string[]
}>()
</script>

<template>
  <ul>
    <slot
      v-for="(item, index) in items"
      :item="item"
      :index="index"
    />
  </ul>
</template>
```

```vue
<!-- app/pages/index.vue -->
<DataList :items="['A', 'B', 'C']">
  <template #default="{ item, index }">
    <li>{{ index + 1 }}. {{ item }}</li>
  </template>
</DataList>
```

### 5.7 Slots Cases Thực Tế Hay Dùng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SLOTS - CASE THỰC TẾ                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CASE 1: WRAPPER/CONTAINER COMPONENT                                │
│  ───────────────────────────────────────                            │
│  <Card><p>Nội dung</p></Card>                                       │
│  Card wrap nội dung, giữ layout nhất quán                           │
│                                                                     │
│  CASE 2: LAYOUT SYSTEM                                              │
│  ───────────────────────────────────────                            │
│  <AppLayout>                                                        │
│    <template #header>...</template>                                 │
│    <template #sidebar>...</template>                                │
│  </AppLayout>                                                       │
│  Header, sidebar, footer - layout động                              │
│                                                                     │
│  CASE 3: LIST COMPONENT                                             │
│  ───────────────────────────────────────                            │
│  <List :items="users">                                              │
│    <template #item="{ item }">...</template>                        │
│  </List>                                                            │
│  Component quản lý list, Parent quyết định render gì                │
│                                                                     │
│  CASE 4: CONDITIONAL SLOT (fallback content)                        │
│  ───────────────────────────────────────                            │
│  <Button>                                                           │
│    <template #icon>...</template>  ← Có thì hiện icon               │
│  </Button>                                                          │
│  Slot có thể trống → component xử lý fallback                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Case 2 - Layout System:**

```vue
<!-- app/components/AppLayout.vue -->
<script setup lang="ts">
defineProps<{
  title: string
}>()
</script>

<template>
  <div class="app-layout">
    <header class="layout-header">
      <slot name="header">
        <h1>{{ title }}</h1>
      </slot>
    </header>

    <div class="layout-body">
      <aside class="layout-sidebar">
        <slot name="sidebar" />
      </aside>

      <main class="layout-content">
        <slot />
      </main>
    </div>

    <footer class="layout-footer">
      <slot name="footer">© 2024 My App</slot>
    </footer>
  </div>
</template>
```

```vue
<!-- app/pages/dashboard.vue -->
<template>
  <AppLayout title="Dashboard">
    <template #header>
      <div class="custom-header">
        <Logo />
        <UserMenu />
      </div>
    </template>

    <template #sidebar>
      <NavMenu />
    </template>

    <!-- Default slot - không cần template -->
    <DashboardContent />
  </AppLayout>
</template>
```

**Case 4 - Conditional Slot với Fallback:**

```vue
<!-- app/components/IconButton.vue -->
<script setup lang="ts">
defineProps<{
  label: string
  variant?: 'primary' | 'secondary'
}>()
</script>

<template>
  <button :class="['icon-btn', `icon-btn-${variant}`]">
    <slot name="icon" />  <!-- Có thì hiện icon -->
    <span>{{ label }}</span>
  </button>
</template>
```

```vue
<!-- app/pages/index.vue -->
<!-- Có icon -->
<IconButton label="Settings">
  <template #icon>
    <IconSettings />
  </template>
</IconButton>

<!-- Không có icon → vẫn hoạt động! -->
<IconButton label="Settings" />
```

---

## 6. Lazy Components

### 6.1 Lazy Components là gì? Dùng để làm gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAZY LOADING - GIẢI THÍCH ĐƠN GIẢN              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  NON-LAZY:                                                          │
│  ├── Tải ngay khi page load                                         │
│  ├── Tất cả JS bundle đều tải trước                                │
│  └── Nếu component nặng (Modal, Chart) → page chậm                 │
│                                                                     │
│  LAZY:                                                              │
│  ├── Chỉ tải khi component được hiển thị                          │
│  ├── Initial JS bundle nhỏ hơn                                      │
│  ├── Page load nhanh hơn                                           │
│  └── Phù hợp cho: Modal, Charts, Heavy components                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Lazy Components dùng để:** Tối ưu performance bằng cách chỉ tải component khi cần.

### 6.2 Lazy Components Có Sẵn Hay Cần Custom?

**LAZY COMPONENTS LÀ TÍNH NĂNG CÓ SẴN CỦA NUXT 4!**

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT TỰ LÀM HẾT CHO BẠN!                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Trong Nuxt 4, mọi component trong thư mục app/components/          │
│  mặc định được auto-imported.                                       │
│                                                                     │
│  NUXT CUNG CẤP SẴN:                                                │
│  Chỉ cần thêm prefix "Lazy" vào tên component:                      │
│                                                                     │
│  ├── <Modal />           → Tải ngay khi page load                   │
│  ├── <LazyModal />       → CHỈ tải khi cần                          │
│                                                                     │
│  └── <HeavyChart />      → Tải ngay (chậm)                         │
│  └── <LazyHeavyChart />  → Tải khi cần (nhanh)                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.3 Cơ Chế Hoạt Động - Behind The Scenes

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT LAZY HOẠT ĐỘNG NHƯ THẾ NÀO?                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. NUXT SCAN THƯ MỤC                                              │
│     Khi build, Nuxt scan thư mục app/components/                    │
│     └── app/components/Modal.vue                                    │
│     └── app/components/HeavyChart.vue                               │
│                                                                     │
│  2. NUXT GENERATE BUNDLES                                          │
│     Nuxt tạo bundles riêng biệt:                                    │
│     ├── main.js (chứa Header, Footer, Sidebar...)                   │
│     ├── Modal.js (chỉ chứa Modal component)                         │
│     └── HeavyChart.js (chỉ chứa HeavyChart component)                │
│                                                                     │
│  3. KHI DÙNG <LazyModal />                                          │
│     Nuxt nhận ra prefix "Lazy" → KHÔNG import vào main bundle       │
│     Chỉ khi component mount (hiển thị) → mới fetch Modal.js          │
│                                                                     │
│  4. CODE SPLITTING TỰ ĐỘNG                                         │
│     NUXT tự động code-split, bạn chỉ cần thêm "Lazy" prefix!      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.4 Cách Sử Dụng - Đơn Giản Nhất

```vue
<!-- app/pages/index.vue -->
<script setup lang="ts">
const showModal = ref(false)
const showChart = ref(false)
const editing = ref(false)
</script>

<template>
  <div>
    <!-- Tải ngay khi page load (non-lazy) -->
    <Header />
    <Sidebar />

    <!-- CHỈ tải khi cần (lazy) - nếu v-if = true thì component mới được tải -->
    <LazyModal v-if="showModal" @close="showModal = false" />
    <LazyHeavyChart v-if="showChart" :data="chartData" />

    <!-- Không có v-if => sẽ bắt đầu tải sau khi component này onMount xong -->
    <!-- Tránh việc phải load quá nhiều thứ cùng một lúc, ưu tiên load các component quan trọng trước -->
    <LazyDataTable :data="tableData" />

    <!-- Các component nặng khác -->
    <LazyRichTextEditor v-if="editing" />
    <LazyImageGallery :images="images" />
  </div>
</template>
```

### 6.5 Các Loại Lazy Loading Trong Nuxt 4

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CÁC LOẠI LAZY LOADING                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. LAZY PREFIX (Nuxt built-in)                                     │
│     <LazyComponentName />                                           │
│     → Chỉ tải khi được mount                                        │
│                                                                     │
│  2. MANUAL DYNAMIC IMPORT                                           │
│     defineAsyncComponent(() => import(...))                         │
│     → Kiểm soát hoàn toàn quá trình tải                             │
│                                                                     │
│  3. LAZY ON VISIBLE (cần module bổ sung)                            │
│     Tải khi component scroll vào viewport                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.6 Manual Lazy với Loading/Error State

```vue
<!-- app/components/AsyncModal.vue -->
<script setup lang="ts">
import { defineAsyncComponent } from 'vue'

const LazyModal = defineAsyncComponent({
  // Component loader
  loader: () => import('./Modal.vue'),

  // Loading component (hiển thị khi đang tải)
  loadingComponent: {
    template: '<div class="loading-spinner">Loading...</div>'
  },

  // Error component (hiển thị khi load fail)
  errorComponent: {
    template: '<div class="error">Failed to load!</div>'
  },

  // Delay trước khi hiện loading (ms)
  delay: 200,

  // Timeout (ms)
  timeout: 3000
})

defineProps<{
  isOpen: boolean
}>()

const emit = defineEmits<{
  close: []
}>()
</script>

<template>
  <div v-if="isOpen">
    <LazyModal @close="emit('close')" />
  </div>
</template>
```

### 6.7 Lazy Cases Thực Tế Hay Dùng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LAZY - CASE THỰC TẾ                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CASE 1: MODALS/POPUPS                                              │
│  ───────────────────────────────────────                            │
│  <LazyModal v-if="show">...</LazyModal>                             │
│  Modal hiếm khi dùng → lazy load cho nhanh                          │
│                                                                     │
│  CASE 2: CHARTS/GRAPHS                                              │
│  ───────────────────────────────────────                            │
│  <LazyChart :data="..." />                                          │
│  Chart.js nặng → chỉ load khi cần hiển thị                          │
│                                                                     │
│  CASE 3: EDITORS (Rich text, Code)                                  │
│  ───────────────────────────────────────                            │
│  <LazyEditor v-if="editing" />                                      │
│  Editor nặng → load khi user click edit                             │
│                                                                     │
│  CASE 4: CONDITIONAL FEATURES                                       │
│  ───────────────────────────────────────                            │
│  <LazyAdminPanel v-if="isAdmin" />                                  │
│  Admin panel chỉ load khi cần                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Case 1 - Modal System:**

```vue
<!-- app/pages/products/[id].vue -->
<script setup lang="ts">
const route = useRoute()
const quickViewProduct = ref<{ id: number } | null>(null)

function openQuickView(product: { id: number }) {
  quickViewProduct.value = product
}
</script>

<template>
  <div class="product-page">
    <ProductInfo :product="product" />

    <!-- Modal - dùng Lazy để không block initial load -->
    <LazyProductQuickView
      v-if="quickViewProduct"
      :product-id="quickViewProduct.id"
      @close="quickViewProduct = null"
    />

    <button @click="openQuickView(product)">
      Quick View
    </button>
  </div>
</template>
```

**Case 3 - Rich Text Editor:**

```vue
<!-- app/pages/posts/[id]/edit.vue -->
<script setup lang="ts">
const post = await useFetch('/api/posts/:id')
const isEditing = ref(false)
const content = ref(post.data.value?.content || '')
</script>

<template>
  <div class="editor-page">
    <PostHeader :post="post.data.value" />

    <!-- Editor nặng - chỉ load khi thực sự edit -->
    <LazyRichTextEditor
      v-if="isEditing"
      v-model="content"
      @save="savePost"
    />

    <div v-else class="content-preview">
      {{ content }}
    </div>

    <button v-if="!isEditing" @click="isEditing = true">
      Edit
    </button>
  </div>
</template>
```

### 6.8 So Sánh Non-Lazy vs Lazy

```vue
<!-- NON-LAZY - Tất cả tải cùng lúc -->
<template>
  <div>
    <Header />                    <!-- Tải ngay → 50KB -->
    <ProductList />               <!-- Tải ngay → 100KB -->
    <Modal />                     <!-- Tải ngay → 80KB (dù có thể không bao giờ dùng) -->
    <HeavyChart />                <!-- Tải ngay → 200KB -->
    <Footer />                    <!-- Tải ngay → 20KB -->
    <!-- Tổng: 450KB → Page load chậm! -->
  </div>
</template>

<!-- LAZY - Chỉ tải thứ cần thiết -->
<template>
  <div>
    <Header />                    <!-- Tải ngay → 50KB -->
    <ProductList />               <!-- Tải ngay → 100KB -->
    <LazyModal v-if="show" />     <!-- Tải khi cần → 80KB -->
    <LazyHeavyChart v-if="show" /> <!-- Tải khi cần → 200KB -->
    <Footer />                    <!-- Tải ngay → 20KB -->
    <!-- Tổng initial: 170KB → Page load NHANH! -->
  </div>
</template>
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPONENTS CHEAT SHEET                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TẠO COMPONENT:                                                     │
│  ├── File: app/components/Button.vue                                │
│  ├── Name: PascalCase                                               │
│  └── Auto-import: <Button />                                       │
│                                                                     │
│  PROPS (Parent → Child):                                            │
│  ├── defineProps<{ name: string }>()                                │
│  ├── withDefaults(defineProps<{...}>(), {})                        │
│  ├── Dùng: :prop-name="value"                                      │
│  └── ⚠️ Props là read-only, muốn thay đổi → emit                  │
│                                                                     │
│  EMITS (Child → Parent):                                            │
│  ├── defineEmits<{ event: [payload] }>()                            │
│  ├── Lắng nghe: @event="handler"                                   │
│  ├── v-model cần emit('update:modelValue', value)                   │
│  └── ⚠️ Để Parent cập nhật state, không sửa trực tiếp             │
│                                                                     │
│  SLOTS (Truyền template):                                           │
│  ├── Default: <slot /> → <Child>content</Child>                     │
│  ├── Named: <slot name="x" /> → <template #x>...</template>        │
│  ├── Scoped: <slot :data="d" /> → <template #="{ data }">...</template> │
│  └── Fallback: <slot>default content</slot>                         │
│                                                                     │
│  LAZY COMPONENTS:                                                   │
│  ├── Nuxt built-in: thêm prefix Lazy                               │
│  ├── <LazyModal /> → tải khi mount                                  │
│  ├── Manual: defineAsyncComponent(() => import(...))               │
│  └── Dùng cho: Modal, Chart, Editor, Heavy components               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [03-routing-basics.md](03-routing-basics.md) - Routing cơ bản

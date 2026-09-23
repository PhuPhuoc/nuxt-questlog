# Templates & Directives - Templates và Directives

> **Mục tiêu:** Hiểu Vue template syntax và directives.

## Mục lục

1. [Template Syntax](#1-template-syntax)
2. [v-bind](#2-v-bind)
3. [v-on](#3-v-on)
4. [v-if vs v-show](#4-v-if-vs-v-show)
5. [v-for](#5-v-for)

---

## 1. Template Syntax

```vue
<template>
  <!-- Text interpolation -->
  <p>{{ message }}</p>
  
  <!-- Raw HTML -->
  <div v-html="htmlContent"></div>
  
  <!-- Attributes -->
  <a :href="url">Link</a>
  <img :src="imageSrc" :alt="imageAlt">
  
  <!-- Classes -->
  <div :class="{ active: isActive }">Content</div>
  
  <!-- Styles -->
  <div :style="{ color: textColor, fontSize: fontSize + 'px' }">Text</div>
</template>
```

---

## 2. v-bind

```vue
<!-- Full syntax -->
<component v-bind:prop="value" />

<!-- Shorthand -->
<component :prop="value" />

<!-- Dynamic attribute name -->
<component :[attrName]="value" />

<!-- Multiple attributes -->
<component v-bind="objectOfAttrs" />
```

---

## 3. v-on

```vue
<!-- Full syntax -->
<button v-on:click="handler">Click</button>

<!-- Shorthand -->
<button @click="handler">Click</button>

<!-- Event modifiers -->
<form @submit.prevent="onSubmit">...</form>
<button @click.stop="handler">...</button>
<input @keyup.enter="submit">

<!-- Dynamic event -->
<component @[eventName]="handler" />
```

### 3.1 Event Modifiers

| Modifier | Mô tả |
|----------|--------|
| `.stop` | event.stopPropagation() |
| `.prevent` | event.preventDefault() |
| `.capture` | capture mode |
| `.self` | chỉ khi target là chính element |
| `.once` | chỉ trigger một lần |
| `.enter` | key modifier |

---

## 4. v-if vs v-show

```
┌─────────────────────────────────────────────────────────────────────┐
│                    v-if vs v-show                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  v-if                                                            │
│  ├── Thêm/xóa element khỏi DOM                                    │
│  ├── Conditional on initial render                                  │
│  ├── Dùng v-else-if, v-else                                       │
│  └── Tốt cho: Logic phức tạp, ít thay đổi                         │
│                                                                     │
│  v-show                                                          │
│  ├── Toggle visibility bằng CSS (display: none)                    │
│  ├── Luôn trong DOM                                               │
│  └── Tốt cho: Toggle thường xuyên                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```vue
<!-- v-if: Remove from DOM -->
<div v-if="isLoggedIn">Welcome!</div>
<div v-else>Please login</div>

<!-- v-show: Toggle CSS display -->
<div v-show="isVisible">Toggle me</div>
```

---

## 5. v-for

### 5.1 Basic

```vue
<!-- Array -->
<li v-for="item in items" :key="item.id">
  {{ item.name }}
</li>

<!-- With index -->
<li v-for="(item, index) in items" :key="item.id">
  {{ index + 1 }}. {{ item.name }}
</li>

<!-- Object -->
<li v-for="(value, key, index) in object" :key="key">
  {{ index + 1 }}. {{ key }}: {{ value }}
</li>
```

### 5.2 With Destructuring

```vue
<li v-for="({ id, name }, index) in items" :key="id">
  {{ index + 1 }}. {{ name }}
</li>
```

---

## ▶️ Tham Khảo

→ [Vue 3 Template](https://vuejs.org/guide/essentials/template-syntax.html)

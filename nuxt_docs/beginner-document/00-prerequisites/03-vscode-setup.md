# VS Code Setup - Công Cụ Lập Trình Nuxt

> **Tóm tắt:** Hướng dẫn cài đặt VS Code và extensions cần thiết cho Nuxt development.

## Mục lục

1. [Tại sao dùng VS Code?](#1-tại-sao-dùng-vs-code)
2. [Cài đặt VS Code](#2-cài-đặt-vs-code)
3. [Extensions cần thiết](#3-extensions-cần-thiết)
4. [Cấu hình VS Code cho Nuxt](#4-cấu-hình-vs-code-cho-nuxt)
5. [Mẹo sử dụng](#5-mẹo-sử-dụng)

---

## 1. Tại Sao Dùng VS Code?

```
┌─────────────────────────────────────────────────────────────────────┐
│                        VS CODE CHO NUXT                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Miễn phí, đa nền tảng                                         │
│  ✅ Extensions tuyệt vời cho Vue/Nuxt                             │
│  ✅ IntelliSense cho TypeScript                                    │
│  ✅ Built-in terminal                                               │
│  ✅ Git integration                                                │
│  ✅ Nuxt DevTools (trình duyệt)                                   │
│                                                                     │
│  ALTERNATIVES:                                                     │
│  ├── WebStorm - Trả phí, mạnh hơn                               │
│  ├── JetBrains Fleet - Mới, nhẹ                                  │
│  └── Vim/Neovim - Dành cho hardcore devs                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Cài Đặt VS Code

### Download và cài đặt

1. **Download:** [code.visualstudio.com](https://code.visualstudio.com/)
2. **Cài đặt:** Chạy installer, làm theo hướng dẫn
3. **Mở VS Code:** Sau khi cài xong

### Extensions để cài từ VS Code Marketplace

Nhấn `Ctrl+Shift+X` (Windows) hoặc `Cmd+Shift+X` (Mac) để mở Extensions panel.

---

## 3. Extensions Cần Thiết

### 🔴 Extensions BẮT BUỘC

#### 3.1 Vue - Official Extension

```
Tên: Vue - Official
Extension ID: Vue.volar
Mô tả: Vue 3 + TypeScript support từ official Vue team
Link: https://marketplace.visualstudio.com/items?itemName=Vue.volar
```

**Tính năng:**
- ✅ Syntax highlighting cho Vue
- ✅ IntelliSense cho Vue APIs
- ✅ TypeScript support
- ✅ Component navigation

#### 3.2 Nuxt - Official Extension

```
Tên: Nuxt
Extension ID: Nuxt.nuxt-vscode
Mô tả: Nuxt module auto-imports, snippets, và IntelliSense
Link: https://marketplace.visualstudio.com/items?itemName=Nuxt.nuxt-vscode
```

**Tính năng:**
- ✅ Auto-complete cho Nuxt APIs
- ✅ Navigate giữa pages/routes
- ✅ Snippets cho Nuxt code

#### 3.3 TypeScript Vue Plugin

```
Tên: TypeScript Vue Plugin (Volar)
Extension ID: Vue.vscode-typescript-vue-plugin
Mô tả: TypeScript plugin cho Vue files
Link: https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin
```

### 🟡 Extensions Khuyến Nghị

#### 3.4 Auto Close Tag

```
Tên: Auto Close Tag
Extension ID: formulahendry.auto-close-tag
Tự động đóng tags HTML/XML
```

#### 3.5 Auto Rename Tag

```
Tên: Auto Rename Tag
Extension ID: formulahendry.auto-rename-tag
Tự động rename tag đóng khi đổi tag mở
```

#### 3.6 ESLint

```
Tên: ESLint
Extension ID: dbaeumer.vscode-eslint
Kiểm tra và fix linting errors
```

#### 3.7 Prettier

```
Tên: Prettier - Code formatter
Extension ID: esbenp.prettier-vscode
Tự động format code
```

#### 3.8 GitLens

```
Tên: GitLens — Git supercharged
Extension ID: eamodio.gitlens
Xem ai viết dòng code đó, lịch sử git trực quan
```

#### 3.9 One Dark Pro (Theme)

```
Tên: One Dark Pro
Extension ID: zhuangtongfa.material-theme-oceanic
Theme tối cho mắt dễ chịu
```

#### 3.10 Error Lens

```
Tên: Error Lens
Extension ID: usernamehw.errorlens
Hiển thị inline errors thay vì chỉ sidebar
```

---

## 4. Cấu Hình VS Code Cho Nuxt

### Tạo file `.vscode/settings.json`

Tạo folder `.vscode` và file `settings.json` trong project:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "[vue]": {
    "editor.defaultFormatter": "Vue.volar"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "typescript.tsdk": "node_modules/typescript/lib"
}
```

### File `.vscode/extensions.json` (Khuyến nghị extensions)

```json
{
  "recommendations": [
    "Vue.volar",
    "Nuxt.nuxt-vscode",
    "Vue.vscode-typescript-vue-plugin",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode"
  ]
}
```

### Cấu hình ESLint

Tạo file `.eslintrc.cjs` trong project:

```javascript
module.exports = {
  root: true,
  extends: [
    'eslint:recommended'
  ],
  parserOptions: {
    ecmaVersion: 2022,
    sourceType: 'module'
  },
  env: {
    browser: true,
    node: true,
    es2022: true
  },
  rules: {
    'no-console': 'warn',
    'no-unused-vars': 'warn'
  }
}
```

### Cấu hình Prettier

Tạo file `.prettierrc` trong project:

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "none",
  "printWidth": 100,
  "vueIndentScriptAndStyle": false
}
```

---

## 5. Mẹo Sử Dụng

### Shortcuts quan trọng

| Shortcut | Chức năng |
|----------|------------|
| `Ctrl+P` | Quick open file |
| `Ctrl+Shift+P` | Command palette |
| `Ctrl+`` | Toggle terminal |
| `Ctrl+B` | Toggle sidebar |
| `Ctrl+Shift+E` | Explorer |
| `Ctrl+Shift+G` | Git |
| `Ctrl+Shift+X` | Extensions |
| `F1` | Command palette |
| `Alt+Click` | Multi-cursor |
| `Ctrl+D` | Select next occurrence |
| `Ctrl+Shift+L` | Select all occurrences |

### Vue/HTML Snippets hữu ích

```javascript
// Khi gõ trong Vue file:
vue          → Vue 3 template cơ bản
scriptSetup  → <script setup> template
v3Composition → Vue 3 Composition API template

// Trong script:
ref         → const name = ref('')
computed    → const name = computed(() => {})
watch       → watch(count, (newVal, oldVal) => {})
onMounted   → onMounted(() => {})
defineProps → defineProps<{ msg: string }>()
```

### Nuxt DevTools (Trình duyệt)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NUXT DEVTOOLS                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nuxt DevTools là công cụ DEV trong TRÌNH DUYỆT                  │
│  Bật: trong nuxt.config.ts                                        │
│                                                                     │
│  nuxt.config.ts:                                                   │
│  export default defineNuxtConfig({                                 │
│    devtools: { enabled: true }                                   │
│  })                                                               │
│                                                                     │
│  Mở: Nhấn Shift + D (hoặc Shift + I) trong trình duyệt          │
│                                                                     │
│  DevTools cho phép:                                                │
│  ├── Xem routes                                                   │
│  ├── Xem Pinia stores                                             │
│  ├── Xem data fetching                                            │
│  ├── Inspect components                                           │
│  ├── Xem layouts                                                  │
│  └── Debug pages                                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Debug trong VS Code

Tạo file `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Nuxt Dev",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal"
    }
  ]
}
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CHECKLIST - VS CODE SETUP                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  □ Đã cài VS Code                                                 │
│  □ Đã cài Vue - Official (Volar)                                │
│  □ Đã cài Nuxt extension                                          │
│  □ Đã cài Prettier                                               │
│  □ Đã cài ESLint                                                 │
│  □ Đã tạo .vscode/settings.json                                  │
│  □ Đã bật Nuxt DevTools                                           │
│                                                                     │
│  NEXT STEPS:                                                      │
│  ├── Tiếp tục với bài tiếp theo                                │
│  └── Bắt đầu tạo Nuxt app                                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [01-getting-started/01-installation.md](../01-getting-started/01-installation.md) - Cài đặt Nuxt và tạo project đầu tiên

---

**Bạn đã sẵn sàng? Bắt đầu học Nuxt! 🚀**

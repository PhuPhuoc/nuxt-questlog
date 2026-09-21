# Mẹo Debug Nâng Cao

> **Mục tiêu:** Học các kỹ thuật debug nâng cao trong Nuxt.

## Mục lục

1. [Debug với Console](#1-debug-với-console)
2. [Debug với VS Code](#2-debug-với-vs-code)
3. [Debug Network Requests](#3-debug-network-requests)
4. [Debug SSR](#4-debug-ssr)
5. [Debug Performance](#5-debug-performance)

---

## 1. Debug với Console

### 1.1 Console Tips

```typescript
// Xem giá trị reactive
const count = ref(0)
console.log(count)        // Ref object
console.log(count.value) // Actual value

// Watch thay đổi
watch(count, (newVal, oldVal) => {
  console.log(`count: ${oldVal} → ${newVal}`)
})

// Debug reactive
import { isRef, isReactive } from 'vue'
console.log(isRef(count))    // true
console.log(isReactive(obj)) // true
```

### 1.2 Console Groups

```typescript
console.group('User Login')
console.log('Email:', email)
console.log('Password:', password)
console.groupEnd()

// Collapsible
console.groupCollapsed('API Response')
console.log(data)
console.groupEnd()
```

### 1.3 Console Styling

```typescript
console.log('%c Debugging Nuxt', 'color: #42b883; font-size: 20px')
console.log('%c Warning', 'color: orange', 'message')
console.log('%c Error', 'color: red', 'message')
```

---

## 2. Debug với VS Code

### 2.1 Launch Configuration

Tạo file `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Nuxt",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "skipFiles": ["<node_internals>/**"],
      "console": "integratedTerminal"
    },
    {
      "type": "node",
      "request": "attach",
      "name": "Attach to Nuxt",
      "port": 9229,
      "restart": true
    }
  ]
}
```

### 2.2 Breakpoints

```
┌─────────────────────────────────────────────────────────────────────┐
│                    USING BREAKPOINTS                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Mở file cần debug                                            │
│  2. Click vào gutter (số dòng bên trái)                         │
│  3. Breakpoint đỏ xuất hiện                                     │
│  4. Chạy Debug (F5)                                              │
│  5. Khi code chạy đến breakpoint → PAUSE                        │
│  6. Inspect variables trong Debug Console                        │
│                                                                     │
│  TYPES:                                                           │
│  • Red dot       → Normal breakpoint                              │
│  • Orange dot    → Conditional breakpoint                         │
│  • Green dot     → Logpoint (không pause, chỉ log)              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.3 Watch Variables

```
1. Mở Debug Panel (Ctrl+Shift+D)
2. Chọn "Watch" section
3. Click + → Thêm biến cần watch
4. VD: count.value, user.name
```

---

## 3. Debug Network Requests

### 3.1 Xem API Calls

```
┌─────────────────────────────────────────────────────────────────────┐
│                    NETWORK TAB IN BROWSER                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Mở DevTools → Network tab                                    │
│  2. Refresh page                                                 │
│  3. Xem tất cả requests:                                          │
│     • XHR/Fetch: API calls                                       │
│     • Doc: HTML, CSS, JS                                        │
│     • Img: Images                                                │
│                                                                     │
│  CLICK VÀO REQUEST ĐỂ XEM:                                       │
│  • Headers: Request/Response headers                              │
│  • Payload: Data gửi đi                                          │
│  • Response: Data nhận về                                        │
│  • Timing: Thời gian                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Copy as cURL

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COPY AS CURL                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Right-click vào request trong Network tab                     │
│  2. Chọn "Copy" → "Copy as cURL"                                 │
│  3. Paste vào terminal để test                                   │
│                                                                     │
│  Ví dụ:                                                          │
│  curl 'http://localhost:3000/api/users' \                         │
│    -H 'Accept: application/json' \                                 │
│    -H 'Content-Type: application/json'                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Debug SSR

### 4.1 Server Logs

```typescript
// Server-side console.log hiển thị trong terminal
// (không phải browser console)

console.log('This runs on SERVER')
console.log('Params:', route.params)
console.log('Query:', route.query)
```

### 4.2 Xem SSR Output

```typescript
// Thêm console.log vào page
const route = useRoute()
console.log('SSR Route:', route.path)
console.log('SSR Params:', route.params)

// Xem trong terminal khi dev server chạy
```

### 4.3 Debug Hydration

```typescript
// Trong app.vue hoặc plugin
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('app:created', () => {
    console.log('App created (SSR)')
  })

  nuxtApp.hook('page:finish', () => {
    console.log('Page finished (CSR)')
  })
})
```

---

## 5. Debug Performance

### 5.1 Vue Performance DevTools

```
1. Cài Vue DevTools extension
2. Mở DevTools → Vue tab
3. Xem Performance recordings
4. Tìm components render chậm
```

### 5.2 Chrome Performance Tab

```
1. Mở DevTools → Performance tab
2. Record (Ctrl+E)
3. Thực hiện actions
4. Stop
5. Analyze flame chart
```

### 5.3 Lighthouse

```
1. Mở DevTools → Lighthouse tab
2. Click "Analyze page load"
3. Xem report:
   • Performance score
   • FCP, LCP, CLS
   • Suggestions
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DEBUG CHECKLIST                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  BASIC:                                                          │
│  ├── console.log() - Giá trị cơ bản                              │
│  ├── Nuxt DevTools - Overview                                      │
│  ├── Browser DevTools - Network/Console                           │
│                                                                     │
│  INTERMEDIATE:                                                   │
│  ├── Vue DevTools - Component inspection                          │
│  ├── Pinia DevTools - State debugging                             │
│  ├── Breakpoints - VS Code                                        │
│                                                                     │
│  ADVANCED:                                                        │
│  ├── SSR debugging - Server logs                                  │
│  ├── Performance profiling                                       │
│  ├── Lighthouse audit                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [04-migration/01-vue2-to-nuxt.md](../04-migration/01-vue2-to-nuxt.md) - Migration từ Vue 2

hoặc → [02-core-concepts/01-auto-imports.md](../02-core-concepts/01-auto-imports.md) - Quay lại học Core Concepts

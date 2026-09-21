# Node.js Cơ Bản - Kiến Thức Cần Biết

> **Tóm tắt:** Phần này giải thích những gì bạn cần biết về Node.js để làm việc với Nuxt.

## Mục lục

1. [Node.js là gì?](#1-nodejs-là-gì)
2. [npm/yarn/pnpm là gì?](#2-npmyarnpnpm-là-gì)
3. [package.json](#3-packagejson)
4. [Cài đặt và chạy packages](#4-cài-đặt-và-chạy-packages)

---

## 1. Node.js là gì?

### Định nghĩa đơn giản

```
┌─────────────────────────────────────────────────────────────────────┐
│                        NODE.JS                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  JavaScript chạy ở ĐÂU?                                            │
│                                                                     │
│  TRƯỚC KHI:                                                        │
│  ├── JavaScript = Chạy trong TRÌNH DUYỆT                          │
│  ├── Browser cung cấp: DOM, BOM, window                           │
│  └── Ví dụ: document.getElementById()                              │
│                                                                     │
│  SAU KHI NODE.JS:                                                  │
│  ├── JavaScript chạy ở SERVER (máy tính)                        │
│  ├── Node cung cấp: File system, Network, OS                      │
│  └── Ví dụ: fs.readFile(), http.createServer()                  │
│                                                                     │
│  NUXT CẦN NODE.JS ĐỂ:                                            │
│  ├── Chạy development server                                       │
│  ├── Build production app                                          │
│  └── Xử lý SSR (Server-Side Rendering)                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Kiểm tra Node.js đã cài chưa

```bash
# Kiểm tra phiên bản Node.js
node -v

# Kiểm tra phiên bản npm
npm -v

# Nếu chưa cài, tải tại: https://nodejs.org/
# Chọn LTS version (bản ổn định)
```

### Phiên bản Node.js khuyến nghị

| Nuxt Version | Node.js tối thiểu |
|-------------|-------------------|
| Nuxt 4 | Node.js 18+ |
| Nuxt 3 | Node.js 14+ |

---

## 2. npm/yarn/pnpm là gì?

### Package Manager là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PACKAGE MANAGER                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Package = Thư viện/code người khác viết                         │
│  Package Manager = Công cụ QUẢN LÝ packages                        │
│                                                                     │
│  Ví dụ packages:                                                    │
│  ├── Vue          → Framework                                       │
│  ├── Nuxt         → Framework (built on Vue)                      │
│  ├── Pinia        → State management                               │
│  ├── Tailwind CSS → CSS framework                                  │
│  └── Axios        → HTTP client                                    │
│                                                                     │
│  Package Manager làm gì?                                            │
│  ├── Cài đặt packages (download + install)                       │
│  ├── Gỡ packages                                                   │
│  ├── Quản lý phiên bản                                            │
│  └── Tự động cài dependencies (packages mà package đó cần)    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### So sánh npm vs yarn vs pnpm

| Tính năng | npm | yarn | pnpm |
|-----------|-----|------|------|
| **Tốc độ** | Chậm | Nhanh | Nhanh nhất |
| **Dung lượng** | Nặng | Nặng | Nhẹ |
| **Mặc định** | Node.js đi kèm | Facebook | Shop |
| **Khuyến nghị Nuxt** | ✅ | ✅ | ✅ |

### Cách cài đặt

```bash
# npm - đi kèm Node.js (đã có sẵn)
# Kiểm tra:
npm -v

# yarn - cài thêm
npm install -g yarn
# Hoặc: corepack enable (Node.js 16+)

# pnpm - cài thêm
npm install -g pnpm

# Bun - cài thêm (Nhanh nhất!)
# Xem hướng dẫn: https://bun.sh
```

### Khuyến nghị cho Nuxt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHUYẾN NGHỊ                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  🏆 SỐ 1: Bun (Nhanh nhất)                                       │
│     curl -fsSL https://bun.sh/install | bash                        │
│     # Hoặc: npm install -g bun                                   │
│                                                                     │
│  🥈 SỐ 2: pnpm (Nhẹ, nhanh)                                      │
│     npm install -g pnpm                                            │
│                                                                     │
│  🥉 SỐ 3: npm (Mặc định, ai cũng có)                            │
│     # Không cần cài thêm                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. package.json

### package.json là gì?

`package.json` là file chứa thông tin về project của bạn:

```json
{
  "name": "my-nuxt-app",
  "version": "1.0.0",
  "description": "My first Nuxt app",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "nuxt dev",
    "build": "nuxt build",
    "generate": "nuxt generate",
    "preview": "nuxt preview",
    "postinstall": "nuxt prepare"
  },
  "dependencies": {
    "nuxt": "^4.0.0",
    "vue": "^3.5.0"
  },
  "devDependencies": {
    "@nuxt/devtools": "^2.0.0"
  }
}
```

### Giải thích từng phần

```json
{
  "name": "my-app",           // Tên project (không có khoảng trắng)
  "version": "1.0.0",         // Phiên bản (semantic versioning)
  "scripts": {
    "dev": "nuxt dev"        // Chạy: npm run dev
  },
  "dependencies": {           // Packages CẦN ĐỂ CHẠY
    "nuxt": "^4.0.0"
  },
  "devDependencies": {        // Packages CHỈ CẦN KHI DEV
    "typescript": "^5.0.0"
  }
}
```

### Scripts thường dùng

```bash
# Development
npm run dev          # Chạy dev server (http://localhost:3000)
npm run dev -- --port 8080  # Chạy port khác

# Build
npm run build        # Build production app
npm run generate    # Generate static site (SSG)

# Production
npm run preview     # Preview production build

# Utility
npm run lint        # Kiểm tra code style
npm run test        # Chạy tests
```

---

## 4. Cài Đặt và Chạy Packages

### Cài đặt package

```bash
# Cú pháp chung
npm install <package-name>

# Ví dụ: Cài Nuxt
npm install nuxt

# Cài với phiên bản cụ thể
npm install nuxt@4.0.0
npm install nuxt@latest

# Cài như dev dependency (chỉ cần khi dev)
npm install -D @nuxt/devtools

# Cài global (dùng ở mọi project)
npm install -g nuxt
```

### package-lock.json và node_modules

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SAU KHI CHẠY npm install                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📁 project/                                                       │
│  ├── 📄 package.json                                              │
│  ├── 📄 package-lock.json  ← Ghi lại phiên bản CHÍNH XÁC       │
│  │                                                               │
│  └── 📁 node_modules/     ← Folder chứa TẤT CẢ packages       │
│      ├── 📁 nuxt/                                                    │
│      ├── 📁 vue/                                                   │
│      └── 📁 ... (hàng trăm folders khác)                          │
│                                                                     │
│  ⚠️ KHÔNG xóa node_modules!                                      │
│  ⚠️ KHÔNG sửa package-lock.json!                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Sự khác biệt dependencies vs devDependencies

```json
{
  "dependencies": {
    // CẦN để CHẠY app (production)
    "nuxt": "^4.0.0",           // ✅ Luôn cài
    "vue": "^3.5.0",            // ✅ Luôn cài
    "pinia": "^2.0.0"            // ✅ Luôn cài
  },
  "devDependencies": {
    // CHỈ cần khi PHÁT TRIỂN
    "@nuxt/devtools": "^2.0.0", // ❌ Không cần khi deploy
    "typescript": "^5.0.0",     // ❌ Không cần khi deploy
    "vitest": "^1.0.0"          // ❌ Không cần khi deploy
  }
}
```

### Khi nào dùng --save-dev?

```bash
# Khi cài tool cho development (linters, testers, builders)
npm install -D typescript
npm install -D eslint
npm install -D @nuxt/devtools

# Khi cài library cần cho app (runtime)
npm install nuxt
npm install pinia
npm install axios
```

---

## 🎯 Tóm Tắt

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CHECKLIST - NODE.JS                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  □ Đã cài Node.js 18+                                            │
│  □ Biết npm/yarn/pnpm là gì                                       │
│  □ Hiểu package.json                                               │
│  □ Biết cài package: npm install <name>                          │
│  □ Biết chạy script: npm run <script-name>                        │
│                                                                     │
│  COMMAND REFERENCE:                                                 │
│  ─────────────────                                                  │
│  node -v           → Kiểm tra Node version                         │
│  npm install       → Cài packages từ package.json                  │
│  npm install <pkg> → Cài một package                               │
│  npm run dev      → Chạy development server                        │
│  npm run build     → Build production                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ▶️ Tiếp Theo

→ [03-vscode-setup.md](03-vscode-setup.md) - Cài đặt VS Code cho Nuxt

hoặc → [Bỏ qua](01-getting-started/01-installation.md) nếu đã hiểu rõ

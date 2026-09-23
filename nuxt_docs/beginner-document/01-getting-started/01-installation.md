# Bước 1: Cài Đặt Nuxt

> **Mục tiêu:** Sau bài này, bạn sẽ cài đặt được Nuxt và chạy ứng dụng đầu tiên.

## Mục lục

1. [Yêu cầu trước khi cài](#1-yêu-cầu-trước-khi-cài)
2. [Cài đặt Bun (Khuyến nghị)](#2-cài-đặt-bun-khuyến-nghị)
3. [Tạo Nuxt Project](#3-tạo-nuxt-project)
4. [Cấu trúc thư mục ban đầu](#4-cấu-trúc-thư-mục-ban-đầu)
5. [Chạy Development Server](#5-chạy-development-server)
6. [Troubleshooting](#6-troubleshooting)

---

## 1. Yêu Cầu Trước Khi Cài

### 1.1 Kiểm tra hệ thống

```bash
# Kiểm tra Node.js
node -v
# Phải >= 18.0.0

# Kiểm tra npm
npm -v
# Phải >= 9.0.0
```

### 1.2 Nếu chưa có Node.js

1. **Download:** [nodejs.org](https://nodejs.org/)
2. **Chọn:** LTS (Long Term Support)
3. **Cài đặt:** Chạy installer
4. **Kiểm tra lại:**

```bash
node -v    # Nên thấy: v20.x.x hoặc v18.x.x
npm -v     # Nên thấy: 9.x.x hoặc cao hơn
```

---

## 2. Cài Đặt Bun (Khuyến Nghị)

### 2.1 Bun là gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                           BUN                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Bun = Package manager MỚI, NHANH hơn npm/yarn                    │
│                                                                     │
│  Ưu điểm:                                                         │
│  ├── Nhanh hơn npm 10-100 lần                                     │
│  ├── Cài đặt dependencies nhanh                                   │
│  ├── Chạy TypeScript không cần compile                              │
│  └── Tương thích với npm scripts                                   │
│                                                                     │
│  ✅ Khuyến nghị cho Nuxt vì tốc độ dev nhanh                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2 Cài đặt Bun

#### Windows (PowerShell)

```powershell
# PowerShell (Run as Administrator)
irm bun.sh/install.ps1 | iex
```

#### Mac/Linux

```bash
# Terminal
curl -fsSL https://bun.sh/install | bash
```

#### Kiểm tra Bun

```bash
bun --version
# Nên thấy: 1.x.x
```

### 2.3 Dùng Bun với Nuxt

```bash
# Thay vì npm:
bun install

# Dùng:
bun run dev
bun run build
bun run preview
```

---

## 3. Tạo Nuxt Project

### 3.1 Cách 1: Dùng nuxi (Khuyến nghị)

```bash
# Với Bun (Nhanh nhất)
bunx nuxi@latest init my-nuxt-app

# Với npm
npx nuxi@latest init my-nuxt-app

# Với pnpm
pnpm dlx nuxi@latest init my-nuxt-app
```

### 3.2 Sau khi tạo project

```bash
# Di chuyển vào thư mục project
cd my-nuxt-app

# Cài dependencies
bun install   # hoặc npm install
```

### 3.3 Cách 2: Clone template

```bash
# Clone minimal template
git clone https://github.com/nuxt/starter.git my-nuxt-app
cd my-nuxt-app
bun install
```

### 3.4 Cách 3: Tạo thủ công

```bash
# Tạo folder
mkdir my-nuxt-app
cd my-nuxt-app

# Tạo package.json
npm init -y

# Cài Nuxt
bun add nuxt

# Tạo nuxt.config.ts
touch nuxt.config.ts
```

---

## 4. Cấu Trúc Thư Mục Ban Đầu

### 4.1 Sau khi tạo, bạn sẽ thấy:

```
📁 my-nuxt-app/
├── 📄 .gitignore
├── 📄 .npmrc
├── 📄 README.md
├── 📄 nuxt.config.ts       ← Cấu hình Nuxt
├── 📄 package.json
├── 📄 tsconfig.json
├── 📄 app/
│   ├── 📄 app.vue         ← Root component
│   └── 📄 pages/
│       └── 📄 index.vue  ← Trang chủ
└── 📄 node_modules/       ← Dependencies
```

### 4.2 Giải thích từng file quan trọng

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FILES TRONG NUXT PROJECT                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  nuxt.config.ts           ← Cấu hình Nuxt                         │
│  ├── Chọn modules                                                        │
│  ├── Cấu hình devtools                                               │
│  └── Thiết lập rendering mode                                        │
│                                                                     │
│  app/app.vue             ← Root component của app                    │
│  ├── NuxtApp được mount vào đây                                    │
│  └── Thường chứa NuxtLayout + NuxtPage                             │
│                                                                     │
│  app/pages/index.vue     ← Trang chủ (/)                            │
│  ├── Đây là PAGE ĐẦU TIÊN                                        │
│  └── File trong pages/ = Route                                      │
│                                                                     │
│  package.json           ← Dependencies và scripts                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Chạy Development Server

### 5.1 Lệnh để chạy

```bash
# Với Bun (Nhanh)
bun run dev

# Với npm
npm run dev
```

### 5.2 Output khi thành công

```
➜  Nuxt 4.5.2
➜  Local: http://localhost:3000/
➜  Ready in 1.5s

  ✓ Nuxt DevTools is enabled (https://devtools.nuxt.com)

  ▸ watching for file changes...

  ➜  press h + enter to show health tips
```

### 5.3 Truy cập ứng dụng

1. **Mở trình duyệt:** [http://localhost:3000](http://localhost:3000)
2. **Bạn sẽ thấy:** Trang chào mừng Nuxt

### 5.4 Nuxt DevTools

```
┌─────────────────────────────────────────────────────────────────────┐
│                      NUXT DEVTOOLS                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Mở DevTools:                                                      │
│  ├── Windows/Linux: Nhấn Shift + D trong trình duyệt             │
│  └── Mac: Nhấn Shift + D                                          │
│                                                                     │
│  DevTools cho phép xem:                                           │
│  ├── Routes - Tất cả routes của app                              │
│  ├── Components - Component tree                                 │
│  ├── Pinia - State management                                    │
│  ├── Page resources - Data fetching                              │
│  └── Modules - Installed Nuxt modules                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. Troubleshooting

### 6.1 Lỗi thường gặp

#### Lỗi: "command not found: nuxt"

```bash
# Fix: Cài lại dependencies
bun install
```

#### Lỗi: "Port 3000 is already in use"

```bash
# Fix 1: Dùng port khác
bun run dev -- --port 3001

# Fix 2: Kill process đang dùng port
# Windows:
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# Mac/Linux:
lsof -i :3000
kill -9 <PID>
```

#### Lỗi: "Cannot find module 'nuxt'"

```bash
# Fix: Cài dependencies lại
rm -rf node_modules package-lock.json
bun install
```

#### Lỗi: "Node.js version too old"

```bash
# Kiểm tra version
node -v

# Update Node.js:
# Windows: Tải và cài lại từ nodejs.org
# Mac: brew install node@20
# Linux: nvm install 20
```

### 6.2 Nếu vẫn không được

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CÁC BƯỚC KHẮC PHỤC CUỐI CÙNG                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Xóa node_modules và package-lock.json:                        │
│     rm -rf node_modules package-lock.json                         │
│                                                                     │
│  2. Cài lại dependencies:                                          │
│     bun install                                                    │
│                                                                     │
│  3. Clear Nuxt cache:                                              │
│     rm -rf .nuxt .output                                          │
│                                                                     │
│  4. Thử chạy lại:                                                  │
│     bun run dev                                                    │
│                                                                     │
│  5. Nếu vẫn lỗi, hỏi trong Nuxt Discord:                         │
│     discord.gg/nuxt                                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ✅ Checklist - Đã Hoàn Thành?

```
□ Đã kiểm tra Node.js >= 18
□ Đã cài Bun (Khuyến nghị)
□ Đã chạy nuxi init my-nuxt-app
□ Đã chạy bun install
□ Đã chạy bun run dev
□ Đã mở http://localhost:3000
□ Đã thấy trang Nuxt welcome
```

---

## ▶️ Tiếp Theo

→ [02-first-app.md](02-first-app.md) - Tạo Ứng Dụng Đầu Tiên

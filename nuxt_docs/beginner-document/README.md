# Hướng Dẫn Học Nuxt 4 - Dành Cho Người Mới

> **Phiên bản:** Nuxt 4 (v4.5.2) | **Framework:** Vue 3.5 | **Yêu cầu:** Biết Vue cơ bản

---

## 🎯 Mục Tiêu Bộ Tài Liệu

Bộ tài liệu này được thiết kế **RIÊNG CHO** người hoàn toàn mới về Nuxt, đã biết Vue cơ bản:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    TRƯỚC KHI HỌC NUXT                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ✅ Bạn ĐÃ BIẾT:                                                 │
│     ├── HTML, CSS, JavaScript cơ bản                              │
│     ├── Vue 3 cơ bản (components, directives)                     │
│     ├── Biết dùng npm/yarn/pnpm                                    │
│     └── Hiểu khái niệm reactive data                              │
│                                                                     │
│  ⏸️ Nếu bạn CHƯA biết Vue, học Vue trước!                        │
│     → Xem: vuejs.org → Getting Started                            │
│                                                                     │
│  📚 Tài liệu này GIẢ ĐỊNH bạn đã biết Composition API cơ bản   │
│     → Nếu chưa, xem: [00-prerequisites/01-vue3-composition-api.md]│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📚 Cấu Trúc Tài Liệu Mới

```
📁 docs/
│
├── 📁 00-prerequisites/           ← KIẾN THỨC CẦN CÓ
│   ├── 📄 01-vue3-composition-api.md   ← [QUAN TRỌNG] Vue Composition API
│   ├── 📄 02-node-basics.md           ← Kiến thức Node.js cơ bản
│   └── 📄 03-vscode-setup.md          ← Cài đặt VS Code cho Nuxt
│
├── 📁 01-getting-started/        ← BẮT ĐẦU
│   ├── 📄 01-installation.md           ← Cài đặt Nuxt
│   ├── 📄 02-first-app.md             ← [MỚI] Hello World step-by-step
│   └── 📄 03-nuxt-structure.md       ← Giải thích cấu trúc thư mục
│
├── 📁 02-core-concepts/         ← KIẾN THỨC CỐT LÕI
│   ├── 📄 01-auto-imports.md          ← Auto-imports & script setup
│   ├── 📄 02-components.md             ← Components & Props
│   ├── 📄 03-routing-basics.md        ← [MỚI] Routing đơn giản
│   ├── 📄 04-routing-advanced.md      ← Nested routes & Layouts
│   ├── 📄 05-middleware.md             ← Middleware & Auth
│   ├── 📄 06-data-fetching.md         ← Data fetching & SSR
│   └── 📄 07-state-management.md       ← State: ref vs useState vs Pinia
│
├── 📁 03-debugging/              ← [MỚI] DEBUG & TROUBLESHOOTING
│   ├── 📄 01-common-errors.md         ← Lỗi thường gặp
│   ├── 📄 02-devtools-guide.md         ← Hướng dẫn Nuxt DevTools
│   └── 📄 03-debugging-tips.md        ← Mẹo debug
│
├── 📁 04-migration/              ← [MỚI] MIGRATION GUIDES
│   ├── 📄 01-vue2-to-nuxt.md          ← Từ Vue 2 lên Nuxt
│   ├── 📄 02-nuxt3-to-nuxt4.md        ← Từ Nuxt 3 lên Nuxt 4
│   └── 📄 03-common-gotchas.md        ← Những bẫy thường gặp
│
├── 📁 05-exercises/              ← BÀI TẬP THỰC HÀNH
│   ├── 📄 01-beginner/               ← [MỚI] Bài tập cho người mới
│   ├── 📄 02-intermediate/            ← Bài tập trung bình
│   └── 📄 03-advanced/                ← Bài tập nâng cao
│
├── 📁 route/                     ← ROUTING (NÂNG CAO)
│   └── ... (giữ nguyên)
│
├── 📁 pinia/                     ← PINIA STATE MANAGEMENT
│   └── ... (giữ nguyên)
│
└── 📄 README.md (this file)
```

---

## 🚀 Lộ Trình Học Đề Xuất

### Dành cho người hoàn toàn mới (4-6 tuần)

```
TUẦN 1: Prerequisites & Setup
├── Day 1-2: Vue 3 Composition API (nếu chưa biết)
├── Day 3-4: Cài đặt Nuxt + Hello World
└── Day 5-7: Cấu trúc thư mục + Auto-imports

TUẦN 2: Core Concepts
├── Day 8-9: Components & Props
├── Day 10-11: Routing cơ bản
└── Day 12-14: Layouts & Middleware

TUẦN 3: Data & State
├── Day 15-17: Data Fetching
├── Day 18-20: State Management
└── Day 21: Debugging basics

TUẦN 4: Practice
├── Day 22-24: Bài tập Beginner
├── Day 25-28: Mini project
└── Tuần 5-6: Ôn tập + Project thực tế
```

### Thời gian đọc ước tính

| Phần | Thời gian | Ghi chú |
|------|------------|----------|
| Prerequisites | ~2 giờ | Quan trọng nếu chưa biết Composition API |
| Getting Started | ~2 giờ | Làm theo step-by-step |
| Core Concepts | ~8 giờ | Đọc + thực hành |
| Debugging | ~1 giờ | Tham khảo khi cần |
| Migration | ~1 giờ | Tham khảo khi cần |
| **Tổng** | **~14 giờ** | |

---

## 🎓 Học Theo Cách Hiệu Quả

### 1. Đọc → Hiểu → Code

```
1. Đọc lý thuyết (5-10 phút)
   ↓
2. Copy code và chạy thử (10-15 phút)
   ↓
3. Tự viết lại từ đầu (15-20 phút)
   ↓
4. Thay đổi code và observe kết quả (10 phút)
   ↓
5. Ghi chú lại điều mới học
```

### 2. Đừng Nhảy Cóc

```
❌ SAI: Đọc nhanh qua rồi nhảy sang phần khác

✅ ĐÚNG:
1. Hoàn thành prerequisites trước
2. Làm theo Hello World step-by-step
3. Hiểu từng khái niệm trước khi qua phần mới
4. Làm bài tập trước khi học nâng cao
```

### 3. Debug Thường Xuyên

```
✅ Mỗi khi code không chạy:
1. Đọc lỗi trong terminal
2. Copy lỗi → Google
3. Xem phần Debugging trong tài liệu
4. Hỏi trong community nếu bí
```

---

## 🔗 Tài Liệu Tham Khảo

| Nguồn | Link |
|--------|------|
| Nuxt Docs | [nuxt.com/docs](https://nuxt.com/docs) |
| Vue 3 Docs | [vuejs.org/guide](https://vuejs.org/guide) |
| Vue Composition API | [vuejs.org/guide/extras/composition-api-faq](https://vuejs.org/guide/extras/composition-api-faq) |
| Pinia | [pinia.vuejs.org](https://pinia.vuejs.org/) |
| Nuxt DevTools | [devtools.nuxt.com](https://devtools.nuxt.com) |

---

## ❓ FAQ - Câu Hỏi Thường Gặp

**Q: Tôi chưa biết Vue, có học được Nuxt không?**
> A: Không! Học Vue 3 trước (đặc biệt Composition API). Nuxt build trên Vue, nên phải hiểu Vue.

**Q: Tôi dùng Vue 2, có học được Nuxt 4 không?**
> A: Có, nhưng nên học Vue 3 Composition API trước. Xem [Migration Guide](04-migration/01-vue2-to-nuxt.md).

**Q: Có cần TypeScript không?**
> A: Không bắt buộc, nhưng khuyến khích. Nuxt hỗ trợ TypeScript mặc định.

**Q: Dùng npm, yarn hay pnpm?**
> A: Tất cả đều được. Pnpm nhanh hơn, Bun nhanh nhất.

**Q: Học xong có làm được gì?**
> A: Static sites, blogs, e-commerce, SaaS apps, dashboards, APIs...

---

## ✅ Checklist Trước Khi Bắt Đầu

```
□ Đã biết HTML, CSS, JavaScript cơ bản
□ Đã biết Vue 3 cơ bản (components, directives)
□ Đã hiểu Composition API (ref, computed, watch)
□ Đã cài Node.js 18+
□ Đã cài VS Code
□ Đã đọc Prerequisites section
```

---

**Bạn đã sẵn sàng? Bắt đầu từ [00-prerequisites/01-vue3-composition-api.md](00-prerequisites/01-vue3-composition-api.md)**

---

*Lần cập nhật: 2024 | Phiên bản: Nuxt 4*

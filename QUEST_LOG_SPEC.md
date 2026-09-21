# Todo Quest Log - App Specification

> Một todo app lấy cảm hứng từ "quest/quest log" - biến công việc thành những thử thách thú vị.

## 🎯 Concept

**Quest Log** - Thay vì "todo list" nhàm chán, biến tasks thành "quests" với:
- **Quest Boards** = Projects/Categories (như Kanban boards)
- **Quests** = Tasks với priority levels
- **Progress tracking** = Stats và completion rate

---

## ✨ Features

### 1. Multiple Quest Boards
- Tạo/xóa/sửa boards (như Trello lists)
- Mỗi board có màu sắc riêng
- Board có thể archived

### 2. Quests (Tasks)
- **Title** - Tên quest
- **Description** - Mô tả chi tiết
- **Priority Levels**:
  - ⭐ Easy (1 sao) - xanh lá
  - ⭐⭐ Medium (2 sao) - vàng
  - ⭐⭐⭐ Hard (3 sao) - đỏ
- **Status**: `todo` | `in-progress` | `done`
- **Due Date** với countdown (VD: "Còn 2 ngày", "Quá hạn 1 ngày")
- **Tags** (tùy chọn)

### 3. Filtering & Search
- Filter theo status (Tất cả / Đang làm / Hoàn thành)
- Filter theo priority
- Search theo title

### 4. Statistics Dashboard
- Tổng số quests
- Đã hoàn thành / Đang làm / Chưa làm
- Completion rate %
- Board có nhiều quests nhất

### 5. Theme Toggle
- Light/Dark mode
- Persist vào localStorage

### 6. Smooth Animations
- Fade-in khi thêm quest mới
- Strikethrough + fade khi complete
- Slide khi xóa

---

## 🏗️ Tech Stack (based on docs)

| Feature | Technology | Doc Reference |
|---------|------------|---------------|
| Multiple pages | File-based routing | `03-routing-basics.md` |
| Layout with sidebar | `layouts/default.vue` | `04-routing-advanced.md` |
| Create/Edit forms | Components + Props/Emits | `02-components.md` |
| Quest CRUD | Pinia Store | `07-state-management.md` |
| Theme toggle | useState + composable | `07-state-management.md` |
| Filtering/Search | Computed properties | `01-auto-imports.md` |
| Data persistence | useCookie hoặc localStorage | `06-data-fetching.md` |
| Route params | Dynamic routes | `03-routing-basics.md` |

---

## 📁 Project Structure

```
app/
├── layouts/
│   └── default.vue          # Layout chính (header, sidebar, main)
├── pages/
│   ├── index.vue            # Dashboard - overview stats
│   ├── board/
│   │   ├── index.vue       # Tất cả boards
│   │   └── [id].vue        # Chi tiết 1 board
│   └── quest/
│       ├── [id].vue        # Chi tiết quest
│       └── new.vue         # Tạo quest mới
├── components/
│   ├── QuestCard.vue        # Hiển thị 1 quest
│   ├── QuestForm.vue        # Form tạo/sửa quest
│   ├── BoardCard.vue        # Hiển thị 1 board
│   ├── BoardForm.vue        # Form tạo/sửa board
│   ├── FilterBar.vue        # Search & filter
│   ├── StatsCard.vue        # Stats component
│   ├── PriorityBadge.vue    # Badge hiển thị priority
│   └── StatusSelect.vue     # Dropdown chọn status
├── composables/
│   ├── useQuests.ts        # Logic quản lý quests
│   ├── useBoards.ts        # Logic quản lý boards
│   ├── useTheme.ts         # Theme toggle logic
│   └── useFilters.ts       # Filter/search logic
├── stores/
│   └── questStore.ts       # Pinia store cho tất cả data
└── utils/
    └── formatters.ts       # Format date, priority labels
```

---

## 🔧 Implementation Phases

### Phase 1: Foundation
- [ ] Setup project structure
- [ ] Create Pinia store với mock data
- [ ] Create `useTheme` composable
- [ ] Setup default layout

### Phase 2: Boards
- [ ] Board list page (`/board`)
- [ ] Board detail page (`/board/[id]`)
- [ ] BoardForm component
- [ ] BoardCard component

### Phase 3: Quests
- [ ] Quest CRUD operations
- [ ] QuestForm component
- [ ] QuestCard component với animations
- [ ] Priority & Status components

### Phase 4: Features
- [ ] Filter & Search
- [ ] Stats Dashboard
- [ ] Due date countdown
- [ ] Confirm delete dialog

### Phase 5: Polish
- [ ] Page transitions
- [ ] Loading states
- [ ] Empty states
- [ ] Responsive design

---

## 📚 Knowledge Coverage Map

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LEARNING PATH                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  PHASE 1 - Basics:                                                 │
│  ├── app.vue + layouts → Hiểu cấu trúc Nuxt                      │
│  ├── ref(), computed() → State management cơ bản                   │
│  └── Components → Tạo components đầu tiên                         │
│                                                                     │
│  PHASE 2 - Routing:                                                │
│  ├── File-based routing → Tạo pages                               │
│  ├── Dynamic routes [id] → Board detail page                       │
│  ├── NuxtLink → Navigation giữa pages                             │
│  └── definePageMeta → Page metadata                                │
│                                                                     │
│  PHASE 3 - Props & Emits:                                          │
│  ├── defineProps → Truyền data vào component                       │
│  ├── defineEmits → Gửi event ra ngoài                             │
│  └── Slots → Nội dung linh hoạt                                   │
│                                                                     │
│  PHASE 4 - State:                                                  │
│  ├── Pinia Store → Centralized state                              │
│  ├── useState → Shared state (theme)                              │
│  └── Composables → Logic tái sử dụng                              │
│                                                                     │
│  PHASE 5 - Advanced:                                               │
│  ├── Middleware → Auth guard (nếu cần)                            │
│  ├── Page transitions → Animations                                 │
│  └── Layouts → Nested layouts                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🎨 Design Direction

- **Style**: Clean, minimal với subtle gamification (sao ⭐, badges)
- **Colors**:
  - Primary: Purple (#7c3aed) - "quest" vibe
  - Success: Green (#10b981)
  - Warning: Yellow (#f59e0b)
  - Danger: Red (#ef4444)
- **Dark mode**: Deep purple-tinted dark theme
- **Typography**: Clean sans-serif (Inter hoặc system fonts)

---

## ⚠️ Out of Scope (for learning)

- Backend API / Server routes (chỉ dùng mock data)
- Authentication / Login
- Real-time collaboration
- Drag & drop (cần thư viện ngoài)
- Push notifications
- Deploy/CI-CD

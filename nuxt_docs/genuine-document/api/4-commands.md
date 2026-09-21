# Commands

## nuxt add

### Mô Tả

Thêm modules vào ứng dụng Nuxt của bạn bằng command line.

### Cú Pháp

```bash
npx nuxt add <MODULENAME> [--cwd=<directory>] [--logLevel=<silent|info|verbose>] [--skipInstall] [--skipConfig] [--dev]
```

### Arguments

**MODULENAME** — Chỉ định một hoặc nhiều modules để cài đặt theo tên, phân cách bằng dấu cách

### Options

- **--cwd=<directory>** (mặc định: `.`) — Chỉ định thư mục làm việc
- **--logLevel=<silent|info|verbose>** — Chỉ định build-time log level
- **--skipInstall** — Bỏ qua npm install
- **--skipConfig** — Bỏ qua cập nhật nuxt.config.ts
- **--dev** — Cài đặt modules như dev dependencies

### Mô Tả

Lệnh cho phép bạn cài đặt Nuxt modules trong ứng dụng của bạn mà không cần thao tác thủ công.

Khi chạy lệnh, nó sẽ:

- Cài đặt module như dependency sử dụng package manager của bạn (trừ khi `--skipInstall` được đặt)
- Thêm vào file package.json (trừ khi `--skipInstall` được đặt)
- Cập nhật file nuxt.config (trừ khi `--skipConfig` được đặt)

Nếu không có module name được truyền, bạn sẽ được nhắc để tìm kiếm và chọn modules để thêm.

### Ví Dụ

Cài đặt module Pinia:

```bash
npx nuxt add pinia
```

:::note
`nuxt module add` là alias cho `nuxt add`.
:::

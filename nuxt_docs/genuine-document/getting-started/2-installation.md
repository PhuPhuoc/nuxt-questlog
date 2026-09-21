# Cài Đặt

Bắt đầu với Nuxt nhanh chóng với các starter trực tuyến hoặc bắt đầu cục bộ với terminal của bạn.

## Chơi Trực Tuyến

Nếu bạn chỉ muốn thử Nuxt trong trình duyệt mà không cần thiết lập project, bạn có thể sử dụng sandbox trực tuyến này:

Open on StackBlitz - Playground trực tuyến với code editor và terminal.

## Tạo Project Mới

### Yêu Cầu Tiên Quyết

- **Node.js** - 22.x hoặc mới hơn (nhưng chúng tôi khuyến nghị phiên bản LTS đang hoạt động)
- **Text editor** - Không có yêu cầu về IDE, nhưng chúng tôi khuyến nghị Visual Studio Code với extension Vue chính thức (trước đây gọi là Volar) hoặc WebStorm, cùng với các IDE JetBrains khác, cung cấp hỗ trợ Nuxt tuyệt vời ngay từ đầu. Nếu bạn sử dụng editor khác, như Neovim, bạn có thể cấu hình hỗ trợ Vue Language Server bằng cách làm theo các hướng dẫn thiết lập Vue Language Tools.
- **Terminal** - Để chạy các lệnh Nuxt

**Các ghi chú bổ sung cho thiết lập tối ưu:**

- **Node.js**: Đảm bảo sử dụng phiên bản số chẵn (22, 24, v.v.)
- **Neovim**: Khi cấu hình plugin Vue TypeScript, đảm bảo location trỏ đến thư mục package `@vue/language-server`, không phải binary của nó. Xem hướng dẫn thiết lập Neovim để có cấu hình hoạt động.
- **WSL**: Nếu bạn đang sử dụng Windows và gặp HMR chậm, bạn có thể muốn thử WSL (Windows Subsystem for Linux) để giải quyết một số vấn đề hiệu năng.
- **Windows slow DNS resolution**: Thay vì sử dụng localhost:3000 cho dev server cục bộ trên Windows, hãy sử dụng 127.0.0.1 để có trải nghiệm tải nhanh hơn trên trình duyệt.

### Tạo Project Mới

Mở terminal (nếu bạn đang sử dụng Visual Studio Code, bạn có thể mở terminal tích hợp) và sử dụng lệnh sau để tạo một starter project mới:

```bash
# npm
npm create nuxt@latest <project-name>

# yarn
yarn create nuxt <project-name>

# pnpm
pnpm create nuxt@latest <project-name>

# bun
bun create nuxt@latest <project-name>

# deno
deno -A npm:create-nuxt@latest <project-name>
```

Mở thư mục project trong Visual Studio Code:

```bash
code <project-name>
```

Hoặc di chuyển vào thư mục project mới từ terminal:

```bash
cd <project-name>
```

### Development Server

Bây giờ bạn có thể khởi động ứng dụng Nuxt ở chế độ phát triển:

```bash
# npm
npm run dev -- -o

# yarn
yarn dev --open

# pnpm
pnpm dev -o

# bun
bun run dev -o
# Để sử dụng Bun runtime trong phát triển
# bun --bun run dev -o

# deno
deno run dev -o
```

Hoàn thành! Một cửa sổ trình duyệt sẽ tự động mở tại http://localhost:3000.

Nếu bạn đang sử dụng Docker hoặc muốn môi trường phát triển nhất quán trên các máy, hãy cân nhắc sử dụng dev container.

Learn more about dev containers

## Các Bước Tiếp Theo

Bây giờ bạn đã tạo một project Nuxt, bạn đã sẵn sàng bắt đầu xây dựng ứng dụng của mình.

Đọc thêm về Các khái niệm của Nuxt

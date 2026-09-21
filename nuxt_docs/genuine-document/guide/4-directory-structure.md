# Directory Structure

## Root Directory

Root directory của một Nuxt application là directory chứa file `nuxt.config.ts`.

## App Directory

Thư mục `app/` là thư mục chính của Nuxt application.

### Các thư mục con:
- `assets/` - assets của website được build tool (Vite hoặc webpack) xử lý
- `components/` - Vue components của ứng dụng
- `composables/` - Thêm Vue composables
- `layouts/` - Vue components wrap around pages
- `middleware/` - Chạy code trước khi navigate đến một route cụ thể
- `pages/` - File-based routing để tạo routes
- `plugins/` - Sử dụng Vue plugins
- `utils/` - Thêm functions có thể sử dụng trong components, composables và pages

### Các files:
- `app.config.ts` - Cấu hình reactive trong ứng dụng
- `app.vue` - Root component của ứng dụng
- `error.vue` - Error page của ứng dụng

## Public Directory

Thư mục `public/` chứa các public files của ứng dụng. Các files được phục vụ tại root và không bị modify bởi build process.

## Server Directory

Thư mục `server/` chứa server-side code của ứng dụng.

### Các thư mục con:
- `api/` - Chứa API routes của ứng dụng
- `routes/` - Chứa server routes (ví dụ dynamic `/sitemap.xml`)
- `middleware/` - Chạy code trước khi server route được process
- `plugins/` - Sử dụng plugins khi tạo Nitro server
- `utils/` - Thêm functions cho server code

## Shared Directory

Thư mục `shared/` chứa code được chia sẻ giữa Vue app và Nitro server.

## Test Directory

Thư mục `test/` là nơi được recommend cho application tests (unit, Nuxt runtime, và end-to-end).

## Content Directory

Thư mục `content/` được enable bởi Nuxt Content module. Được sử dụng để tạo file-based CMS sử dụng Markdown files.

## Modules Directory

Thư mục `modules/` chứa local modules của ứng dụng.

## Layers Directory

Thư mục `layers/` cho phép organize và chia sẻ reusable code, components, composables và configurations.

## Nuxt Files

- `nuxt.config.ts` - Main configuration file
- `.nuxtrc` - Cú pháp khác để cấu hình Nuxt
- `.Nuxtignore` - Bỏ qua files trong root directory trong build phase

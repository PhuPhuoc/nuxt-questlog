# Deployment

> Tìm hiểu cách deploy ứng dụng Nuxt của bạn lên bất kỳ nhà cung cấp hosting nào.

Một ứng dụng Nuxt có thể được deploy trên server Node.js, pre-rendered cho static hosting, hoặc deploy lên các môi trường serverless hoặc edge (CDN).

## Node.js Server

Khám phá preset Node.js server với Nitro để deploy trên bất kỳ Node hosting nào.

- **Default output format** nếu không có preset nào được chỉ định hoặc auto-detected
- Load chỉ các chunks cần thiết để render request cho optimal cold start timing
- Hữu ích để deploy Nuxt apps lên bất kỳ Node.js hosting nào

### Entry Point

Khi chạy `nuxt build` với Node server preset, kết quả sẽ là một entry point khởi chạy một Node server đã sẵn sàng chạy.

```bash
NODE_ENV=production node .output/server/index.mjs
```

Điều này sẽ khởi chạy production Nuxt server của bạn lắng nghe trên port 3000 theo mặc định.

**Đặt `NODE_ENV=production` khi chạy server.** Một số dependencies (đáng chú ý là Vue Router) chỉ loại bỏ các development-only warnings khi điều này được đặt, vì vậy việc không đặt nó có thể làm ngập logs của bạn với các messages như `[Vue Router warn]: No match found for location with path …` trên các routes không khớp.

Nó tôn trọng các biến môi trường runtime sau:

- `NITRO_PORT` hoặc `PORT` (mặc định là `3000`)
- `NITRO_HOST` hoặc `HOST` (mặc định là `'0.0.0.0'`)
- `NITRO_SSL_CERT` và `NITRO_SSL_KEY` - nếu cả hai đều có, điều này sẽ khởi chạy server ở chế độ HTTPS. Trong đại đa số các trường hợp, điều này không nên được sử dụng ngoài việc testing, và Nitro server nên được chạy đằng sau một reverse proxy như nginx hoặc Cloudflare để terminate SSL.

### Phục Vụ Cùng Một Build Ở Nhiều Paths

Đối với một subpath deployment bình thường, hãy đặt `app.baseURL` hoặc biến môi trường `NUXT_APP_BASE_URL`.

Nếu một reverse proxy cố tình expose cùng một rendered page ở nhiều public paths, Nuxt có thể thay thế browser URL bằng path được sử dụng cho server rendering trong hydration. Bạn có thể giữ browser URL bằng cách xóa rendered path khỏi payload trong một server plugin:

```ts
export default defineNuxtPlugin((nuxtApp) => {
  delete nuxtApp.payload.path
})
```

Sử dụng điều này chỉ khi proxy đã xử lý assets và routing cho mọi public path. Không có rendered path, Nuxt không thể sửa một mismatch thực sự giữa URL được request và route được server-render.

### PM2

PM2 (Process Manager 2) là một giải pháp nhanh và dễ dàng để host ứng dụng Nuxt của bạn trên server hoặc VM.

Để sử dụng `pm2`, sử dụng một `ecosystem.config.cjs`:

```ts
module.exports = {
  apps: [
    {
      name: 'NuxtAppName',
      port: '3000',
      exec_mode: 'cluster',
      instances: 'max',
      script: './.output/server/index.mjs',
      env: {
        NODE_ENV: 'production',
      },
    },
  ],
}
```

### Cluster Mode

Bạn có thể sử dụng `NITRO_PRESET=node_cluster` để tận dụng hiệu năng multi-process sử dụng Node.js cluster module.

Theo mặc định, workload được phân phối đến các workers với chiến lược round robin.

## Static Hosting

Có hai cách để deploy ứng dụng Nuxt lên bất kỳ dịch vụ static hosting nào:

- **Static site generation (SSG)** với `ssr: true` pre-renders các routes của ứng dụng tại thời điểm build. (Đây là hành vi mặc định khi chạy `nuxt generate`.) Nó cũng sẽ tạo các trang `/200.html` và `/404.html` single-page app fallback, có thể render các routes động hoặc lỗi 404 trên client (mặc dù bạn có thể cần cấu hình điều này trên static host của bạn).
- **Hoặc**, bạn có thể pre-render site của mình với `ssr: false` (static single-page app). Điều này sẽ tạo ra các trang HTML với một `<div id="__nuxt"></div>` trống nơi Vue app của bạn thường được render. Bạn sẽ mất nhiều lợi ích SEO của pre-rendering site, vì vậy thay vào đó nên sử dụng `<ClientOnly>` để bọc các phần của site không thể được server render (nếu có).

### Static Fallback Pages

Nuxt có thể tạo hai fallback pages cho static hosts:

- `200.html` là single-page app fallback. Cấu hình host của bạn để phục vụ nó cho các routes không khớp khi bạn muốn client-side routing xử lý URL.
- `404.html` là not-found fallback. Cấu hình host của bạn để phục vụ nó cho các routes nên giữ nguyên status 404.

`nuxt generate` và `nuxt build --prerender` tạo các file này tự động. Nếu bạn sử dụng `nuxt build` với route rules để pre-render các routes đã chọn, thêm fallback page một cách tường minh:

```ts
export default defineNuxtConfig({
  routeRules: {
    '/200.html': { prerender: true },
  },
})
```

### Client-side Only Rendering

Nếu bạn không muốn pre-render các routes của mình, một cách khác để sử dụng static hosting là đặt thuộc tính `ssr` thành `false` trong file `nuxt.config`. Lệnh `nuxt generate` sẽ output một `.output/public/index.html` entrypoint và các JavaScript bundles như một classic client-side Vue.js application.

```ts
export default defineNuxtConfig({
  ssr: false,
})
```

## Hosting Providers

Nuxt có thể được deploy lên một số cloud providers với một lượng cấu hình tối thiểu.

## Presets

Ngoài Node.js servers và static hosting services, một project Nuxt có thể được deploy với một số presets đã được kiểm tra kỹ lưỡng và một lượng cấu hình tối thiểu.

Bạn có thể đặt tường minh preset mong muốn trong file `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  nitro: {
    preset: 'node-server',
  },
})
```

... hoặc sử dụng biến môi trường `NITRO_PRESET` khi chạy `nuxt build`:

```bash
NITRO_PRESET=node-server nuxt build
```

## CDN Proxy

Trong hầu hết các trường hợp, Nuxt có thể hoạt động với nội dung bên thứ ba không được Nuxt tạo ra. Nhưng đôi khi nội dung như vậy có thể gây ra vấn đề, đặc biệt là "Minification and Security Options" của Cloudflare.

Theo đó, bạn nên đảm bảo rằng các tùy chọn sau được bỏ chọn / disabled trong Cloudflare. Nếu không, việc re-rendering không cần thiết hoặc hydration errors có thể ảnh hưởng đến ứng dụng production của bạn.

1. Speed > Settings > Content Optimization > Disable "Rocket Loader™"
2. Security > Settings > Disable "Email Address Obfuscation"

Với các cài đặt này, bạn có thể chắc chắn rằng Cloudflare sẽ không inject scripts vào ứng dụng Nuxt của bạn có thể gây ra các side effects không mong muốn.

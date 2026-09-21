# Key Concepts

## Rendering Modes

Nuxt hỗ trợ các chế độ rendering khác nhau: universal rendering, client-side rendering, hybrid rendering và khả năng render ứng dụng trên CDN Edge Servers.

### Universal Rendering

Cách tiếp cận này tương tự như traditional server-side rendering được thực hiện bởi các ứng dụng PHP hoặc Ruby. Khi trình duyệt yêu cầu một URL với universal rendering enabled, Nuxt chạy JavaScript (Vue.js) code trong server environment và trả về một trang HTML đã được render đầy đủ cho trình duyệt.

Sau khi HTML document được download, trình duyệt diễn giải và Vue.js kiểm soát document. JavaScript code chạy trên server cũng chạy trên client (trình duyệt) trong background, cho phép interactivity (gọi là **Hydration**).

**Benefits:**
- **Performance**: Người dùng có thể truy cập ngay vào nội dung của trang
- **SEO**: Web crawlers có thể index trực tiếp nội dung của trang

**Downsides:**
- **Development constraints**: Server và browser environments không cung cấp cùng APIs
- **Cost**: Server cần chạy để render pages on the fly

### Client-Side Rendering

Một Vue.js application truyền thống được render trong trình duyệt. Vue.js generate HTML elements sau khi trình duyệt download và parse tất cả JavaScript code.

```ts
export default defineNuxtConfig({
  ssr: false,
})
```

**Benefits:**
- **Development speed**: Không cần lo về server compatibility
- **Cheaper**: Không cần server để host
- **Offline**: Có thể hoạt động khi không có internet

**Downsides:**
- **Performance**: Người dùng phải đợi download, parse và run JavaScript
- **SEO**: Indexing mất nhiều thời gian hơn

### Hybrid Rendering

Hybrid rendering cho phép các quy tắc caching khác nhau cho mỗi route sử dụng **Route Rules**.

```ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },
    '/products/**': { swr: 3600 },
    '/blog/**': { isr: 3600 },
    '/admin/**': { ssr: false },
  },
})
```

### Edge-Side Rendering

Edge-Side Rendering (ESR) cho phép render ứng dụng Nuxt gần với người dùng hơn qua edge servers của CDN.

Các nền tảng hỗ trợ ESR:
- Cloudflare Pages
- Vercel Cloud
- Netlify Edge Functions

## Server Engine

Nuxt được cung cấp sức mạnh bởi một server engine mới: **Nitro**.

### Tính Năng

- Cross-platform support cho Node.js, browsers, service workers
- Serverless support out-of-the-box
- API routes support
- Automatic code-splitting và async-loaded chunks
- Hybrid mode cho static + serverless sites
- Development server với hot module reloading

### API Layer

Server API endpoints và Middleware được thêm bởi Nitro sử dụng h3 bên trong.

### Direct API Calls

Nitro cho phép 'direct' calling của routes qua globally-available `$fetch` helper.

### Typed API Routes

Khi sử dụng API routes, Nitro sẽ generate typings cho các routes này.

### Standalone Server

Nitro produces một standalone server dist độc lập với node_modules.

## TypeScript

Nuxt hoàn toàn được type và cung cấp các shortcuts hữu ích.

### Type-checking

```bash
npx nuxt typecheck
```

### Auto-generated Types

Nuxt projects dựa vào các types được auto-generated lưu trong thư mục `.nuxt`.

### Project References

Nuxt sử dụng TypeScript project references để cải thiện type-checking performance.

Các file được generate:
- `.nuxt/tsconfig.app.json` - Cho application code trong thư mục app/
- `.nuxt/tsconfig.node.json` - Cho nuxt.config.ts
- `.nuxt/tsconfig.server.json` - Cho server-side code
- `.nuxt/tsconfig.shared.json` - Cho code chia sẻ giữa app và server

### Strict Checks

```ts
export default defineNuxtConfig({
  typescript: {
    strict: false,
  },
})
```

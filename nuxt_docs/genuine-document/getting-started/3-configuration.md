# Cấu Hình

Nuxt được cấu hình với các giá trị mặc định hợp lý để giúp bạn làm việc hiệu quả.

## Cấu Hình Nuxt

File `nuxt.config.ts` nằm ở root của project Nuxt và có thể ghi đè hoặc mở rộng hành vi của ứng dụng.

Một file cấu hình tối thiểu export function `defineNuxtConfig` chứa một object với cấu hình của bạn. Helper `defineNuxtConfig` có sẵn toàn cục mà không cần import.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  // My Nuxt config
})
```

File này thường được đề cập trong tài liệu, ví dụ để thêm scripts tùy chỉnh, đăng ký modules hoặc thay đổi chế độ rendering.

:::tip
Đọc thêm về mọi tùy chọn trong **Configuration Reference**.
:::

:::note
Bạn không cần phải sử dụng TypeScript để xây dựng ứng dụng với Nuxt. Tuy nhiên, chúng tôi strongly recommend sử dụng extension `.ts` cho file `nuxt.config`. Bằng cách này, bạn có thể hưởng lợi từ các gợi ý trong IDE để tránh typos và sai sót khi chỉnh sửa cấu hình.
:::

### Ghi Đè Môi Trường

Bạn có thể cấu hình các ghi đè được type đầy đủ cho từng môi trường trong nuxt.config.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  $production: {
    routeRules: {
      '/**': { isr: true },
    },
  },
  $development: {
    //
  },
  $env: {
    staging: {
      //
    },
  },
})
```

Để chọn một môi trường khi chạy lệnh Nuxt CLI, chỉ cần truyền tên vào flag `--envName`, như sau: `nuxt build --envName staging`.

Để tìm hiểu thêm về cơ chế đằng sau các ghi đè này, vui lòng tham khảo tài liệu `c12` về cấu hình theo môi trường cụ thể.

### Biến Môi Trường và Token Riêng Tư

API `runtimeConfig` expose các giá trị như biến môi trường đến phần còn lại của ứng dụng. Theo mặc định, các key này chỉ khả dụng phía server. Các key trong `runtimeConfig.public` và `runtimeConfig.app` (được Nuxt sử dụng nội bộ) cũng khả dụng phía client.

Các giá trị này nên được định nghĩa trong `nuxt.config` và có thể được ghi đè bằng biến môi trường.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  runtimeConfig: {
    // Các key riêng tư chỉ khả dụng phía server
    apiSecret: '123',
    // Các key trong public cũng được expose phía client
    public: {
      apiBase: '/api',
    },
  },
})
```

```ini [.env]
# Điều này sẽ ghi đè giá trị của apiSecret
NUXT_API_SECRET=api_secret_token
```

Các biến này được expose đến phần còn lại của ứng dụng bằng composable `useRuntimeConfig()`.

```vue [app/pages/index.vue]
<script setup lang="ts">
const runtimeConfig = useRuntimeConfig()
</script>
```

### App Configuration

File `app.config.ts`, nằm trong source directory (theo mặc định là `app/`), được sử dụng để expose các biến công khai có thể được xác định tại thời điểm build. Ngược với tùy chọn `runtimeConfig`, các biến này không thể được ghi đè bằng biến môi trường.

Một file cấu hình tối thiểu export function `defineAppConfig` chứa một object với cấu hình của bạn. Helper `defineAppConfig` có sẵn toàn cục mà không cần import.

```ts [app/app.config.ts]
export default defineAppConfig({
  title: 'Hello Nuxt',
  theme: {
    dark: true,
    colors: {
      primary: '#ff0000',
    },
  },
})
```

Các biến này được expose đến phần còn lại của ứng dụng bằng composable `useAppConfig`.

```vue [app/pages/index.vue]
<script setup lang="ts">
const appConfig = useAppConfig()
</script>
```

### `runtimeConfig` vs `app.config`

Như đã nói ở trên, `runtimeConfig` và `app.config` đều được sử dụng để expose các biến đến phần còn lại của ứng dụng. Để xác định bạn nên sử dụng cái nào, đây là một số hướng dẫn:

- **`runtimeConfig`**: Token riêng tư hoặc công khai cần được chỉ định sau build bằng biến môi trường.
- **`app.config`**: Token công khai được xác định tại thời điểm build, cấu hình website như theme variant, title và bất kỳ cấu hình project nào không nhạy cảm.

**Bảng so sánh:**

| Tính năng | `runtimeConfig` | `app.config` |
|-----------|----------------|--------------|
| Client-side | Hydrated | Bundled |
| Biến môi trường | Có | Không |
| Reactive | Có | Có |
| Hỗ trợ Types | Một phần | Có |
| Cấu hình per request | Không | Có |
| Hot module replacement | Không | Có |
| Non-primitive JS types | Không | Có |

### Các File Cấu Hình Bên Ngoài

Nuxt sử dụng file `nuxt.config.ts` như nguồn thông tin duy nhất cho các cấu hình và bỏ qua việc đọc các file cấu hình bên ngoài. Trong quá trình build project, bạn có thể cần cấu hình những file đó. Bảng sau đây liệt kê các cấu hình phổ biến và cách chúng có thể được cấu hình với Nuxt.

| Tên | File Cấu Hình | Cách Cấu Hình |
|-----|---------------|---------------|
| Nitro | ~~`nitro.config.ts`~~ | Sử dụng key `nitro` trong `nuxt.config` |
| PostCSS | ~~`postcss.config.js`~~ | Sử dụng key `postcss` trong `nuxt.config` |
| Vite | ~~`vite.config.ts`~~ | Sử dụng key `vite` trong `nuxt.config` |
| webpack | ~~`webpack.config.ts`~~ | Sử dụng key `webpack` trong `nuxt.config` |

**Các file cấu hình phổ biến khác:**

| Tên | File Cấu Hình | Thông Tin Thêm |
|-----|---------------|---------------|
| TypeScript | `tsconfig.json` | More Info |
| ESLint | `eslint.config.js` | More Info |
| Prettier | `prettier.config.js` | More Info |
| Stylelint | `stylelint.config.js` | More Info |
| TailwindCSS | `tailwind.config.js` | More Info |
| Vitest | `vitest.config.ts` | More Info |

## Cấu Hình Vue

### Với Vite

Nếu bạn cần truyền các tùy chọn cho `@vitejs/plugin-vue` hoặc `@vitejs/plugin-vue-jsx`, bạn có thể làm điều này trong file `nuxt.config`.

- `vite.vue` cho `@vitejs/plugin-vue`. Kiểm tra các tùy chọn khả dụng.
- `vite.vueJsx` cho `@vitejs/plugin-vue-jsx`. Kiểm tra các tùy chọn khả dụng.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  vite: {
    vue: {
      customElement: true,
    },
    vueJsx: {
      mergeProps: true,
    },
  },
})
```

### Với webpack

Nếu bạn sử dụng webpack và cần cấu hình `vue-loader`, bạn có thể làm điều này bằng key `webpack.loaders.vue` bên trong file `nuxt.config`. Các tùy chọn khả dụng được định nghĩa trong tài liệu Vue Loader.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  webpack: {
    loaders: {
      vue: {
        hotReload: true,
      },
    },
  },
})
```

### Bật các Tính Năng Vue Thực Nghiệm

Bạn có thể cần bật các tính năng thực nghiệm trong Vue, như `propsDestructure`. Nuxt cung cấp một cách dễ dàng để làm điều đó trong `nuxt.config.ts`, bất kể builder nào bạn đang sử dụng:

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  vue: {
    propsDestructure: true,
  },
})
```

# Nuxt Configuration

Khám phá tất cả các tùy chọn bạn có thể sử dụng trong file `nuxt.config.ts`.

## Các Tùy Chọn Cấu Hình Chính

### alias

Định nghĩa thêm aliases cho các custom directories.

```ts
export default defineNuxtConfig({
  alias: {
    'myalias': './mydir'
  }
})
```

**Type:** `object`

### analyzeDir

Thư mục cho các analyze files đã được generate.

**Default:** `"/<rootDir>/.nuxt/analyze"`

### app

Cấu hình Nuxt App với các sub-options:

```ts
export default defineNuxtConfig({
  app: {
    baseURL: '/',
    buildAssetsDir: '/_nuxt/',
    cdnURL: '/',
    head: {
      title: 'Nuxt',
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
      ]
    },
    keepalive: true,
    layoutTransition: { name: 'layout' },
    pageTransition: { name: 'page' }
  }
})
```

**Sub-options:**
- `baseURL`: Base URL của ứng dụng
- `buildAssetsDir`: Thư mục cho build assets
- `cdnURL`: URL của CDN
- `head`: Cấu hình head tags
- `keepalive`: Giữ alive state giữa các pages
- `layoutTransition`: Transition cho layouts
- `pageTransition`: Transition cho pages
- `rootAttrs`: Attributes cho root element
- `rootId`: ID cho root element
- `rootTag`: Tag cho root element
- `viewTransition`: Bật View Transitions API

### builder

Builder để sử dụng: `'vite' | 'webpack' | 'rspack' | string`

**Default:** `"@nuxt/vite-builder"`

```ts
export default defineNuxtConfig({
  builder: 'webpack'
})
```

### css

Global CSS files/modules.

```ts
export default defineNuxtConfig({
  css: ['~/assets/css/main.css']
})
```

**Type:** `string[]`

### devtools

Bật Nuxt DevTools.

```ts
export default defineNuxtConfig({
  devtools: { enabled: true }
})
```

### modules

Nuxt extensions để cài đặt.

```ts
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/tailwindcss',
    '@nuxt/image'
  ]
})
```

**Type:** `(string | NuxtModule)[]`

### runtimeConfig

Config động có thể truy cập qua `useRuntimeConfig`.

```ts
export default defineNuxtConfig({
  runtimeConfig: {
    apiSecret: '123',
    public: {
      apiBase: '/api'
    }
  }
})
```

### srcDir

Source directory của ứng dụng.

**Default:** `"app"` (trong Nuxt 4)

```ts
export default defineNuxtConfig({
  srcDir: 'src/'
})
```

### ssr

Bật SSR.

**Default:** `true`

```ts
export default defineNuxtConfig({
  ssr: false
})
```

### typescript

Cấu hình TypeScript.

```ts
export default defineNuxtConfig({
  typescript: {
    strict: true,
    typeCheck: true
  }
})
```

**Sub-options:**
- `strict`: Bật strict mode
- `typeCheck`: Kiểm tra types trong build
- `tsConfig`: Tùy chỉnh tsconfig path
- `shim`: Tạo shims cho components

### vite

Cấu hình Vite.

```ts
export default defineNuxtConfig({
  vite: {
    server: {
      port: 3000
    },
    css: {
      preprocessorOptions: {
        scss: {
          additionalData: '@use "~/assets/globals.scss" as *;'
        }
      }
    }
  }
})
```

### webpack

Cấu hình webpack.

```ts
export default defineNuxtConfig({
  webpack: {
    analyze: true,
    loaders: {
      vue: {
        hotReload: true
      }
    }
  }
})
```

## Các Tùy Chọn Cấu Hình Khác

### compatibilityDate

Ngày compatibility cho Nuxt.

```ts
export default defineNuxtConfig({
  compatibilityDate: '2024-11-01'
})
```

### devServerUrl

URL của dev server.

```ts
export default defineNuxtConfig({
  devServerUrl: 'http://localhost:3000'
})
```

### experimental

Các tính năng thực nghiệm.

```ts
export default defineNuxtConfig({
  experimental: {
    payloadExtraction: true,
    renderJsonPayloads: true
  }
})
```

### extensions

Extensions cho file resolution.

```ts
export default defineNuxtConfig({
  extensions: ['.vue', '.jsx']
})
```

### future

Các tính năng tương lai được bật.

```ts
export default defineNuxtConfig({
  future: {
    compatibilityVersion: 4
  }
})
```

### future

### generate

Cấu hình cho static generation.

```ts
export default defineNuxtConfig({
  generate: {
    routes: ['/about', '/contact']
  }
})
```

### hooks

Hooks được đăng ký.

```ts
export default defineNuxtConfig({
  hooks: {
    'build:before': () => {
      console.log('Build starting')
    }
  }
})
```

### ignored

Các files/patterns bị ignore trong watching.

```ts
export default defineNuxtConfig({
  ignored: ['**/*.test.vue']
})
```

### inlineStyles

Inline styles trong SSR.

```ts
export default defineNuxtConfig({
  inlineStyles: true
})
```

### modulesDir

Directories cho modules.

```ts
export default defineNuxtConfig({
  modulesDir: ['node_modules']
})
```

### nitro

Cấu hình Nitro server engine.

```ts
export default defineNuxtConfig({
  nitro: {
    preset: 'node-server'
  }
})
```

### postcss

Cấu hình PostCSS.

```ts
export default defineNuxtConfig({
  postcss: {
    plugins: [
      ['autoprefixer', { grid: true }]
    ]
  }
})
```

### routeRules

Route rules cho hybrid rendering.

```ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },
    '/api/**': { cors: true },
    '/admin/**': { ssr: false }
  }
})
```

### serverDir

Server directory.

```ts
export default defineNuxtConfig({
  serverDir: 'server/'
})
```

### terminal

Cấu hình terminal output.

```ts
export default defineNuxtConfig({
  terminal: {
    options: {
      breakpoints: true
    }
  }
})
```

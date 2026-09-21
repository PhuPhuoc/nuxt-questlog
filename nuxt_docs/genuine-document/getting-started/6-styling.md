# Styling

Tìm hiểu cách style ứng dụng Nuxt của bạn.

## Local Stylesheets

Nếu bạn viết các local stylesheets, nơi tự nhiên để đặt chúng là trong thư mục `app/assets/`.

### Import Trong Components

Bạn có thể import stylesheets trong pages, layouts và components của bạn trực tiếp. Bạn có thể sử dụng một JavaScript import, hoặc một CSS `@import` statement.

```vue
<script>
import '~/assets/css/main.css'
</script>
```

Hoặc sử dụng `@import` trong CSS của bạn:

```css
@import "~/assets/css/main.css";
```

:::note
Các import tĩnh được khuyến nghị để tương thích với server-side rendering. Các import động không tương thích server-side.
:::

### Thuộc Tính CSS

Bạn có thể sử dụng thuộc tính `css` trong cấu hình Nuxt. Stylesheets sẽ được inlined trong HTML được render bởi Nuxt, injected toàn cục và hiện diện trong tất cả các pages.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  css: ['~/assets/css/main.css']
})
```

## Làm Việc Với Fonts

Đặt các font files cục bộ trong thư mục `public/` của bạn. Tham chiếu chúng trong stylesheets sử dụng `url()` với các khai báo `@font-face`.

```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/my-font.woff2') format('woff2');
  font-weight: 400;
  font-style: normal;
}
```

## Stylesheets Phân Phối Qua NPM

Bạn có thể tham chiếu các stylesheets phân phối qua npm như animate.css.

```bash
# npm
npm install animate.css

# yarn
yarn add animate.css

# pnpm
pnpm add animate.css

# bun
bun add animate.css

# deno
deno add npm:animate.css
```

Sau đó import chúng:

```ts
import 'animate.css'
```

## External Stylesheets

Thêm các external stylesheets bằng cách đặt một link element trong phần head của file nuxt.config của bạn.

Sử dụng thuộc tính `app.head`:

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  app: {
    head: {
      link: [
        { rel: 'stylesheet', href: 'https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css' }
      ]
    }
  }
})
```

Sử dụng composable `useHead`:

```vue
<script setup lang="ts">
useHead({
  link: [
    { rel: 'stylesheet', href: 'https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css' }
  ]
})
</script>
```

:::note
External stylesheets là các render-blocking resources. Cân nhắc sử dụng chúng một cách tiết kiệm.
:::

## Sử Dụng Preprocessors

Nuxt hỗ trợ SCSS, Sass, Less, và Stylus.

```bash
# npm
npm install -D sass

# yarn
yarn add -D sass

# pnpm
pnpm add -D sass

# bun
bun add -D sass
```

```vue
<style lang="scss">
$primary: #42b883;

.container {
  background: $primary;
}
</style>
```

### Preprocessor Workers (Thực Nghiệm)

Các preprocessor workers có thể tăng tốc quá trình xử lý.

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  experimental: {
    workers: true
  },
  vite: {
    css: {
      preprocessorOptions: {
        scss: {
          workers: true
        }
      }
    }
  }
})
```

## Single File Components (SFC) Styling

Vue SFC cung cấp các khả năng styling tuyệt vời:

### Scoped Styles

```vue
<style scoped>
.button {
  background: blue;
}
</style>
```

### CSS Modules

```vue
<style module>
.button {
  background: blue;
}
</style>
```

### Dynamic Styles với v-bind

```vue
<style>
.button {
  background: v-bind(color);
}
</style>
```

```vue
<script setup lang="ts">
const color = ref('blue')
</script>
```

## Sử Dụng PostCSS

Nuxt bao gồm postcss được cấu hình sẵn với các plugins:

- postcss-import
- postcss-url
- autoprefixer
- cssnano

## Các Thư Viện Và Modules Bên Thứ Ba

- **UnoCSS**: Atomic CSS engine
- **Tailwind CSS**: Utility-first CSS framework
- **Fontaine**: Font metrics tự động
- **Pinceau**: Style system cho Vue
- **Nuxt UI**: Components được style sẵn
- **Panda CSS**: CSS-in-JS engine

## Các Chủ Đề Nâng Cao

- Transitions
- Font optimization
- LCP optimizations

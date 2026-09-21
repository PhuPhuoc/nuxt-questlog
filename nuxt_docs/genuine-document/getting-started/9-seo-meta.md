# SEO và Meta

> Cải thiện SEO của ứng dụng Nuxt với cấu hình head mạnh mẽ, composables và components.

Việc quản lý head tag của Nuxt được cung cấp bởi Unhead. Nó cung cấp các giá trị mặc định hợp lý, một số composables mạnh mẽ và nhiều tùy chọn cấu hình để quản lý head và SEO meta tags của ứng dụng.

## Nuxt Config

Việc cung cấp thuộc tính `app.head` trong `nuxt.config.ts` cho phép bạn tùy chỉnh head một cách tĩnh cho toàn bộ ứng dụng.

:::important
Phương pháp này không cho phép bạn cung cấp dữ liệu reactive. Chúng tôi khuyến nghị sử dụng `useHead()` trong `app.vue`.
:::

Đây là thực hành tốt để đặt các tags ở đây mà sẽ không thay đổi như title mặc định của site, ngôn ngữ và favicon.

```ts
export default defineNuxtConfig({
  app: {
    head: {
      title: 'Nuxt',
      htmlAttrs: {
        lang: 'en',
      },
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
      ],
    },
  },
})
```

:::note
Khi bạn đặt `app.cdnURL`, các assets trong thư mục `public/` (bao gồm favicon.ico) được phục vụ từ CDN đó. Nuxt resolve các public assets against `cdnURL`, fallback về `app.baseURL`. Tuy nhiên, một `app.head` link tĩnh như `href: '/favicon.ico'` ở trên là một literal path và không được resolve against `cdnURL`. Để trỏ favicon đến location đã được resolve, hãy build href từ runtime config với `useHead()` trong `app.vue`.
:::

### Default Tags

Một số tags được Nuxt cung cấp theo mặc định để đảm bảo website của bạn hoạt động tốt ngay từ đầu:

- viewport: width=device-width, initial-scale=1
- charset: utf-8

Trong khi hầu hết các sites sẽ không cần override các giá trị mặc định này, bạn có thể cập nhật chúng bằng các shortcuts được định nghĩa.

```ts
export default defineNuxtConfig({
  app: {
    head: {
      charset: 'utf-16',
      viewport: 'width=device-width, initial-scale=1, maximum-scale=1',
    },
  },
})
```

## useHead

Composable function `useHead` hỗ trợ input reactive, cho phép bạn quản lý các head tags của mình theo chương trình.

```vue
<script setup lang="ts">
useHead({
  title: 'My App',
  meta: [
    { name: 'description', content: 'My amazing site.' },
  ],
  bodyAttrs: {
    class: 'test',
  },
  script: [{ innerHTML: 'console.log(\'Hello world\')' }],
})
</script>
```

Chúng tôi khuyến nghị xem các composables `useHead` và `useHeadSafe`.

## useSeoMeta

Composable `useSeoMeta` cho phép bạn định nghĩa SEO meta tags của site như một object với full type safety.

Điều này giúp bạn tránh typos và các mistakes phổ biến, như sử dụng `name` thay vì `property`.

```vue
<script setup lang="ts">
useSeoMeta({
  title: 'My Amazing Site',
  ogTitle: 'My Amazing Site',
  description: 'This is my amazing site, let me tell you all about it.',
  ogDescription: 'This is my amazing site, let me tell you all about it.',
  ogImage: 'https://example.com/image.png',
  twitterCard: 'summary_large_image',
})
</script>
```

## Components

Trong khi việc sử dụng `useHead` được khuyến nghị trong mọi trường hợp, bạn có thể có sở thích cá nhân để định nghĩa các head tags trong template của mình bằng cách sử dụng components.

Nuxt cung cấp các components sau cho mục đích này: Title, Base, NoScript, Style, Meta, Link, Body, Html và Head. Lưu ý capitalization của các components này để đảm bảo chúng ta không sử dụng các native HTML tags không hợp lệ.

Head và Body có thể chấp nhận các meta tags lồng nhau (vì lý do thẩm mỹ) nhưng điều này không ảnh hưởng đến nơi các meta tags lồng nhau được render trong HTML cuối cùng.

```vue
<script setup lang="ts">
const title = ref('Hello World')
</script>

<template>
  <div>
    <Head>
      <Title>{{ title }}</Title>
      <Meta
        name="description"
        :content="title"
      />
      <Style>
        body { background-color: green; }
      </Style>
    </Head>

    <h1>{{ title }}</h1>
  </div>
</template>
```

:::warning
Nếu bạn cần duplicate tags qua client-server boundaries, hãy apply một thuộc tính key trên component Head.
:::

## Types

Dưới đây là các reactive types được sử dụng cho `useHead`, `app.head` và components.

```ts
interface MetaObject {
  title?: string
  titleTemplate?: string | ((title?: string) => string)
  templateParams?: Record<string, string | Record<string, string>>
  base?: Base
  link?: Link[]
  meta?: Meta[]
  style?: Style[]
  script?: Script[]
  noscript?: Noscript[]
  htmlAttrs?: HtmlAttributes
  bodyAttrs?: BodyAttributes
}
```

## Tính Năng

### Reactivity

Reactivity được hỗ trợ trên tất cả các thuộc tính, bằng cách cung cấp một computed value, một getter, hoặc một reactive object.

```vue
<script setup lang="ts">
const description = ref('My amazing site.')

useHead({
  meta: [
    { name: 'description', content: description },
  ],
})
</script>
```

### Title Template

Bạn có thể sử dụng tùy chọn `titleTemplate` để cung cấp một template động để tùy chỉnh title của site. Ví dụ, bạn có thể thêm tên site vào title của mỗi page.

`titleTemplate` có thể là một string, nơi `%s` được thay thế bằng title, hoặc một function.

```vue
<script setup lang="ts">
useHead({
  titleTemplate: (titleChunk) => {
    return titleChunk ? `${titleChunk} - Site Title` : 'Site Title'
  },
})
</script>
```

### Template Parameters

Bạn có thể sử dụng `templateParams` để cung cấp các placeholders bổ sung trong `titleTemplate` ngoài `%s` mặc định.

```vue
<script setup lang="ts">
useHead({
  titleTemplate: (titleChunk) => {
    return titleChunk ? `${titleChunk} %separator %siteName` : '%siteName'
  },
  templateParams: {
    siteName: 'Site Title',
    separator: '-',
  },
})
</script>
```

### Body Tags

Bạn có thể sử dụng tùy chọn `tagPosition: 'bodyClose'` trên các tags phù hợp để append chúng vào cuối body tag.

```vue
<script setup lang="ts">
useHead({
  script: [
    {
      src: 'https://third-party-script.com',
      tagPosition: 'bodyClose',
    },
  ],
})
</script>
```

Các tùy chọn hợp lệ là: `'head'` | `'bodyClose'` | `'bodyOpen'`

## Ví Dụ

### Với definePageMeta

Trong thư mục `app/pages/` của bạn, bạn có thể sử dụng `definePageMeta` cùng với `useHead` để đặt metadata dựa trên route hiện tại.

```vue
<script setup lang="ts">
definePageMeta({
  title: 'Some Page',
})
</script>
```

```vue
<script setup lang="ts">
const route = useRoute()

useHead({
  meta: [{ property: 'og:title', content: `App Name - ${route.meta.title}` }],
})
</script>
```

### Dynamic Title

```vue
<script setup lang="ts">
useHead({
  titleTemplate: '%s - Site Title',
})
</script>
```

```vue
<script setup lang="ts">
useHead({
  titleTemplate: (productCategory) => {
    return productCategory
      ? `${productCategory} - Site Title`
      : 'Site Title'
  },
})
</script>
```

### External CSS

```vue
<script setup lang="ts">
useHead({
  link: [
    {
      rel: 'preconnect',
      href: 'https://fonts.googleapis.com',
    },
    {
      rel: 'stylesheet',
      href: 'https://fonts.googleapis.com/css2?family=Roboto&display=swap',
      crossorigin: '',
    },
  ],
})
</script>
```

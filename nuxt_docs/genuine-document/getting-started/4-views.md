# Views

> Nuxt cung cấp nhiều lớp component để triển khai giao diện người dùng của ứng dụng.

## `app.vue`

![File app.vue là entry point của ứng dụng](https://nuxt.com/assets/docs/getting-started/views/app.svg)

Theo mặc định, Nuxt sẽ coi file này là **entrypoint** và render nội dung của nó cho mọi route của ứng dụng.

```vue [app/app.vue]
<template>
  <div>
    <h1>Welcome to the homepage</h1>
  </div>
</template>
```

:::tip
Nếu bạn đã quen thuộc với Vue, bạn có thể tự hỏi `main.js` ở đâu (file thường tạo một Vue app). Nuxt làm điều này đằng sau hậu trường.
:::

## Components

![Components là các phần UI có thể tái sử dụng](https://nuxt.com/assets/docs/getting-started/views/components.svg)

Hầu hết các components là các phần có thể tái sử dụng của giao diện người dùng, như buttons và menus. Trong Nuxt, bạn có thể tạo các components này trong thư mục [`app/components/`](https://nuxt.com/docs/4.x/directory-structure/app/components), và chúng sẽ tự động khả dụng trên toàn ứng dụng mà không cần import một cách tường minh.

```vue [app/app.vue]
<template>
  <div>
    <h1>Welcome to the homepage</h1>
    <AppAlert>
      This is an auto-imported component.
    </AppAlert>
  </div>
</template>
```

```vue [app/components/AppAlert.vue]
<template>
  <span>
    <slot />
  </span>
</template>
```

## Pages

![Pages là các view gắn với một route cụ thể](https://nuxt.com/assets/docs/getting-started/views/pages.svg)

Pages đại diện cho các view cho mỗi pattern route cụ thể. Mỗi file trong thư mục [`app/pages/`](https://nuxt.com/docs/4.x/directory-structure/app/pages) đại diện cho một route khác nhau hiển thị nội dung của nó.

Để sử dụng pages, tạo file `app/pages/index.vue` và thêm component `<NuxtPage />` vào [`app/app.vue`](https://nuxt.com/docs/4.x/directory-structure/app/app) (hoặc xóa `app/app.vue` để có entry mặc định). Bây giờ bạn có thể tạo thêm các pages và các route tương ứng của chúng bằng cách thêm các file mới trong thư mục [`app/pages/`](https://nuxt.com/docs/4.x/directory-structure/app/pages).

```vue [app/pages/index.vue]
<template>
  <div>
    <h1>Welcome to the homepage</h1>
    <AppAlert>
      This is an auto-imported component
    </AppAlert>
  </div>
</template>
```

```vue [app/pages/about.vue]
<template>
  <section>
    <p>This page will be displayed at the /about route.</p>
  </section>
</template>
```

::read-more
---
to: https://nuxt.com/docs/4.x/getting-started/routing
title: Routing Section
---
::

## Layouts

![Layouts là wrapper xung quanh pages](https://nuxt.com/assets/docs/getting-started/views/layouts.svg)

Layouts là các wrapper xung quanh pages chứa một giao diện người dùng chung cho nhiều pages, như hiển thị header và footer. Layouts là các file Vue sử dụng component `<slot />` để hiển thị nội dung **page**. File `app/layouts/default.vue` sẽ được sử dụng theo mặc định. Các layouts tùy chỉnh có thể được thiết lập như một phần của page metadata.

:::note
Nếu bạn chỉ có một layout duy nhất trong ứng dụng, chúng tôi khuyến nghị sử dụng [`app/app.vue`](https://nuxt.com/docs/4.x/directory-structure/app/app) với [`<NuxtPage />`](https://nuxt.com/docs/4.x/api/components/nuxt-page) thay thế.
:::

```vue [app/app.vue]
<template>
  <div>
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
```

```vue [app/layouts/default.vue]
<template>
  <div>
    <AppHeader />
    <slot />
    <AppFooter />
  </div>
</template>
```

```vue [app/pages/index.vue]
<template>
  <div>
    <h1>Welcome to the homepage</h1>
    <AppAlert>
      This is an auto-imported component
    </AppAlert>
  </div>
</template>
```

```vue [app/pages/about.vue]
<template>
  <section>
    <p>This page will be displayed at the /about route.</p>
  </section>
</template>
```

Nếu bạn muốn tạo thêm layouts và tìm hiểu cách sử dụng chúng trong pages, hãy tìm thêm thông tin trong [phần Layouts](https://nuxt.com/docs/4.x/directory-structure/app/layouts).

## Nâng Cao: Mở Rộng HTML Template

:::note
Nếu bạn chỉ cần sửa đổi `<head>`, bạn có thể tham khảo [phần SEO và meta](https://nuxt.com/docs/4.x/getting-started/seo-meta).
:::

Bạn có thể kiểm soát hoàn toàn HTML template bằng cách thêm một Nitro plugin đăng ký một hook.
Function callback của hook `render:html` cho phép bạn mutate HTML trước khi nó được gửi đến client.

```ts [server/plugins/extend-html.ts]
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('render:html', (html, { event }) => {
    // Đây sẽ là một object đại diện cho html template.
    console.log(html)
    html.head.push(`<meta name="description" content="My custom description" />`)
  })
  // Bạn cũng có thể intercept response ở đây.
  nitroApp.hooks.hook('render:response', (response, { event }) => { console.log(response) })
})
```

::read-more
---
to: https://nuxt.com/docs/4.x/guide/going-further/hooks
---
::

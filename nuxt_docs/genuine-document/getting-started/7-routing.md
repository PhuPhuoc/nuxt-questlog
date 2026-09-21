# Routing

Nuxt file-system routing tạo một route cho mỗi file trong thư mục pages/.

## Tổng Quan

Một core feature của Nuxt là file system router. Mỗi file Vue bên trong thư mục `app/pages/` tạo ra một URL (hoặc route) tương ứng hiển thị nội dung của file. Bằng cách sử dụng dynamic imports cho mỗi page, Nuxt tận dụng code-splitting để gửi lượng JavaScript tối thiểu cho route được yêu cầu.

Code-splitting được bật theo mặc định và được khuyến nghị cho hầu hết các ứng dụng. Nếu bạn có lý do cụ thể để gửi một bundle duy nhất thay thế, bạn có thể tắt nó trong `nuxt.config`:

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  vite: {
    $client: {
      build: {
        rolldownOptions: {
          output: {
            codeSplitting: false,
          },
        },
      },
    },
  },
})
```

Điều này gửi tất cả JavaScript trong một file duy nhất. Điều này hiếm khi có lợi — nó thường tăng kích thước download ban đầu, ngay cả trên các kết nối chậm — vì vậy chỉ tắt code-splitting nếu bạn đã đo lường rằng nó giúp ích cho trường hợp của bạn.

## Pages

Nuxt routing dựa trên vue-router và tạo các routes từ mọi component được tạo trong thư mục `app/pages/`, dựa trên filename của chúng.

File system routing này sử dụng các quy ước đặt tên để tạo các dynamic và nested routes:

```bash [Cấu Trúc Thư Mục]
-| pages/
---| about.vue
---| index.vue
---| posts/
-----| [id].vue
```

```json [Generated Router File]
{
  "routes": [
    {
      "path": "/about",
      "component": "pages/about.vue"
    },
    {
      "path": "/",
      "component": "pages/index.vue"
    },
    {
      "path": "/posts/:id",
      "component": "pages/posts/[id].vue"
    }
  ]
}
```

## Navigation

Component `<NuxtLink>` liên kết các pages giữa chúng. Nó render một tag `<a>` với thuộc tính `href` được đặt thành route của page. Khi ứng dụng đã được hydrate, các page transitions được thực hiện trong JavaScript bằng cách cập nhật URL của trình duyệt. Điều này ngăn chặn các full-page refreshes và cho phép các animated transitions.

Khi một `<NuxtLink>` đi vào viewport phía client, Nuxt sẽ tự động prefetch các components và payload (các pages đã được tạo) của các linked pages trước, dẫn đến navigation nhanh hơn.

```vue [app/pages/index.vue]
<template>
  <header>
    <nav>
      <ul>
        <li><NuxtLink to="/about">About</NuxtLink></li>
        <li><NuxtLink to="/posts/1">Post 1</NuxtLink></li>
        <li><NuxtLink to="/posts/2">Post 2</NuxtLink></li>
      </ul>
    </nav>
  </header>
</template>
```

## Route Parameters

Composable `useRoute()` có thể được sử dụng trong block `<script setup>` hoặc method `setup()` của một Vue component để truy cập các chi tiết route hiện tại.

```vue [pages/posts/[id].vue]
<script setup lang="ts">
const route = useRoute()

// Khi truy cập /posts/1, route.params.id sẽ là 1
console.log(route.params.id)
</script>
```

## Route Middleware

Nuxt cung cấp một framework route middleware có thể tùy chỉnh mà bạn có thể sử dụng trên toàn ứng dụng, lý tưởng để trích xuất code mà bạn muốn chạy trước khi navigate đến một route cụ thể.

Route middleware chạy trong phần Vue của ứng dụng Nuxt. Mặc dù có tên tương tự, chúng hoàn toàn khác với server middleware, được chạy trong phần Nitro server của ứng dụng.

Route middleware **không** chạy cho các server routes (ví dụ `/api/*`) hoặc các server requests khác. Để áp dụng middleware cho các requests này, hãy sử dụng server middleware thay thế.

Có ba loại route middleware:

1. **Anonymous** (hoặc inline) route middleware, được định nghĩa trực tiếp trong các pages nơi chúng được sử dụng.
2. **Named** route middleware, được đặt trong thư mục `app/middleware/` và sẽ được tự động load qua asynchronous import khi được sử dụng trên một page. (Lưu ý: Tên route middleware được normalized thành kebab-case, vì vậy `someMiddleware` trở thành `some-middleware`.)
3. **Global** route middleware, được đặt trong thư mục `app/middleware/` (với hậu tố `.global`) và sẽ được tự động chạy trên mọi route change.

Ví dụ về một middleware `auth` bảo vệ page `/dashboard`:

```ts [middleware/auth.ts]
function isAuthenticated (): boolean { return false }
// ---cut---
export default defineNuxtRouteMiddleware((to, from) => {
  // isAuthenticated() là một phương thức ví dụ xác minh nếu người dùng đã được xác thực
  if (isAuthenticated() === false) {
    return navigateTo('/login')
  }
})
```

```vue [pages/dashboard.vue]
<script setup lang="ts">
definePageMeta({
  middleware: 'auth',
})
</script>

<template>
  <h1>Welcome to your dashboard</h1>
</template>
```

## Route Validation

Nuxt cung cấp route validation thông qua thuộc tính `validate` trong `definePageMeta()` trong mỗi page bạn muốn validate.

Thuộc tính `validate` chấp nhận `route` như một argument. Bạn có thể return một boolean value để xác định liệu đây có phải là một route hợp lệ để render với page này. Nếu bạn return `false`, điều này sẽ gây ra lỗi 404. Bạn cũng có thể trực tiếp return một object với `status`/`statusText` để tùy chỉnh lỗi được trả về.

Nếu bạn có một use case phức tạp hơn, bạn có thể sử dụng anonymous route middleware thay thế.

```vue [pages/posts/[id].vue]
<script setup lang="ts">
definePageMeta({
  validate (route) {
    // Kiểm tra nếu id chỉ chứa các chữ số
    return typeof route.params.id === 'string' && /^\d+$/.test(route.params.id)
  },
})
</script>
```

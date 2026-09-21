# Components

Danh sách các Components có sẵn trong Nuxt.

## ClientOnly

### Mô Tả

Component `<ClientOnly>` được sử dụng để render một component chỉ trên phía client.

### Props

- `placeholderTag` | `fallbackTag`: chỉ định một tag để render phía server.
- `placeholder` | `fallback`: chỉ định nội dung để render phía server.

### Slots

- `#fallback`: chỉ định nội dung được render trên server và hiển thị cho đến khi `<ClientOnly>` được mount trong trình duyệt.

### Ví Dụ

```vue
<template>
  <div>
    <Sidebar />
    <!-- Component <Comment> sẽ chỉ được render phía client-side -->
    <ClientOnly
      fallback-tag="span"
      fallback="Loading comments..."
    >
      <Comment />
    </ClientOnly>
  </div>
</template>
```

```vue
<template>
  <div>
    <Sidebar />
    <!-- Điều này render element "span" phía server -->
    <ClientOnly fallback-tag="span">
      <!-- component này sẽ chỉ được render phía client -->
      <Comments />
      <template #fallback>
        <!-- điều này sẽ được render phía server -->
        <p>Loading comments...</p>
      </template>
    </ClientOnly>
  </div>
</template>
```

### Truy Cập HTML Elements

Các components bên trong `<ClientOnly>` chỉ được render sau khi được mount. Để truy cập các elements đã render trong DOM, bạn có thể watch một template ref.

```vue
<script setup lang="ts">
const nuxtWelcomeRef = useTemplateRef('nuxtWelcomeRef')

// Watch sẽ được trigger khi component có sẵn
watch(nuxtWelcomeRef, () => {
  console.log('<NuxtWelcome /> mounted')
}, { once: true })
</script>

<template>
  <ClientOnly>
    <NuxtWelcome ref="nuxtWelcomeRef" />
  </ClientOnly>
</template>
```

## NuxtLink

### Mô Tả

Nuxt cung cấp component `<NuxtLink>` để xử lý mọi loại links trong ứng dụng.

`<NuxtLink>` là một replacement cho cả component `<RouterLink>` của Vue Router và tag `<a>` của HTML. Nó thông minh xác định liệu link là *internal* hay *external* và render tương ứng với các optimizations có sẵn (prefetching, default attributes, v.v.).

### Internal Routing

```vue
<template>
  <NuxtLink to="/about">About page</NuxtLink>
</template>
```

### Truyền Params đến Dynamic Routes

```vue
<template>
  <NuxtLink :to="{ name: 'posts-id', params: { id: 123 } }">
    Post 123
  </NuxtLink>
</template>
```

### Xử Lý Static File và Cross-App Links

Theo mặc định, `<NuxtLink>` sử dụng client side navigation của Vue Router cho relative route. Khi liên kết đến static files trong thư mục `/public` hoặc đến một ứng dụng khác trên cùng domain, có thể dẫn đến lỗi 404 không mong muốn. Trong những trường hợp này, bạn có thể sử dụng prop `external`.

#### Liên Kết đến Static Files

```vue
<template>
  <NuxtLink
    to="/example-report.pdf"
    external
  >
    Download Report
  </NuxtLink>
</template>
```

### External Routing

```vue
<template>
  <NuxtLink to="https://nuxtjs.org">
    Nuxt website
  </NuxtLink>
</template>
```

### Thuộc Tính `rel` và `noRel`

Thuộc tính `rel` mặc định là `noopener noreferrer` cho các links có `target` hoặc absolute links.

```vue
<template>
  <NuxtLink to="https://twitter.com/nuxt_js">
    Nuxt Twitter
  </NuxtLink>

  <NuxtLink
    to="https://discord.nuxtjs.org"
    rel="noopener"
  >
    Nuxt Discord
  </NuxtLink>

  <NuxtLink
    to="/about"
    target="_blank"
  >About page</NuxtLink>
</template>
```

### Prefetch Links

Nuxt tự động bao gồm smart prefetching. Nó phát hiện khi một link hiển thị và prefetch JavaScript cho các pages đó.

```vue
<NuxtLink to="/about" no-prefetch>
About page not pre-fetched
</NuxtLink>
```

### Custom Prefetch Triggers

```vue
<template>
  <NuxtLink prefetch-on="visibility">
    Sẽ prefetch khi visible (mặc định)
  </NuxtLink>

  <NuxtLink prefetch-on="interaction">
    Sẽ prefetch khi hovered hoặc focused
  </NuxtLink>
</template>
```

### Props

#### RouterLink

- `to`: URL hoặc route location object
- `custom`: Liệu `<NuxtLink>` có nên bọc nội dung trong `<a>` hay không
- `exactActiveClass`: Class cho exact active links
- `activeClass`: Class cho active links
- `replace`: Thay thế history entry thay vì push
- `ariaCurrentValue`: Giá trị thuộc tính `aria-current`

#### NuxtLink

- `href`: Alias cho `to`
- `noRel`: Không thêm `rel` attribute
- `external`: Force render như `<a>` tag
- `prefetch`: Bật/tắt prefetching
- `prefetchOn`: Kiểm soát khi nào prefetch (`interaction` hoặc `visibility`)
- `noPrefetch`: Tắt prefetching
- `prefetchedClass`: Class cho links đã prefetch

#### Anchor

- `target`: Giá trị thuộc tính `target`
- `rel`: Giá trị thuộc tính `rel`

## NuxtPage

### Mô Tả

Component `NuxtPage` hiển thị các pages trong thư mục `pages/`.

`NuxtPage` là một built-in component đi kèm với Nuxt. Nó cho phép hiển thị top-level hoặc nested pages trong thư mục `app/pages/`.

**Lưu ý:** `NuxtPage` là một wrapper xung quanh `RouterView` từ Vue Router. Nó nên được sử dụng thay vì `RouterView` vì nó chăm sóc các internal states bổ sung.

### Props

- **name**: Render component với tên tương ứng
- **route**: Route location với components đã resolved
- **pageKey**: Kiểm soát khi `NuxtPage` re-render
- **transition**: Define transitions cho tất cả pages
- **keepalive**: Kiểm soát state preservation của pages

### Ví Dụ

```vue
<template>
  <NuxtPage page-key="static" />
</template>
```

```html
<NuxtPage :page-key="route => route.fullPath" />
```

### Page's Ref

```vue
<script setup lang="ts">
const page = ref()

function logFoo () {
  page.value.pageRef.foo()
}
</script>

<template>
  <NuxtPage ref="page" />
</template>
```

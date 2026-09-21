# Assets

Nuxt cung cấp hai tùy chọn cho các assets của bạn.

Nuxt sử dụng hai thư mục để xử lý các assets như stylesheets, fonts hoặc images.

- Thư mục `public/` phục vụ nội dung tại server root như nó vốn có.
- Thư mục `app/assets/` chứa theo quy ước mọi asset mà bạn muốn công cụ build (Vite hoặc webpack) xử lý.

## Thư Mục Public

Thư mục `public/` được sử dụng như một public server cho các static assets có sẵn công khai tại một URL được định nghĩa của ứng dụng.

Bạn có thể lấy một file trong thư mục `public/` từ code ứng dụng hoặc từ trình duyệt bằng URL root `/`.

### Ví Dụ

Ví dụ, tham chiếu một file image trong thư mục `public/img/`, có sẵn tại static URL `/img/nuxt.png`:

```vue [app/app.vue]
<template>
  <img
    src="/img/nuxt.png"
    alt="Discover Nuxt"
  >
</template>
```

## Thư Mục Assets

Nuxt sử dụng Vite (mặc định) hoặc webpack để build và bundle ứng dụng. Chức năng chính của các công cụ build này là xử lý các file JavaScript, nhưng chúng có thể được mở rộng thông qua plugins (cho Vite) hoặc loaders (cho webpack) để xử lý các loại assets khác, như stylesheets, fonts hoặc SVGs. Bước này transform file gốc, chủ yếu cho mục đích hiệu năng hoặc cache (như stylesheet minification hoặc browser cache invalidation).

Theo quy ước, Nuxt sử dụng thư mục `app/assets/` để lưu trữ các file này nhưng không có chức năng auto-scan cho thư mục này, và bạn có thể sử dụng bất kỳ tên nào khác cho nó.

Trong code ứng dụng, bạn có thể tham chiếu một file nằm trong thư mục `app/assets/` bằng cách sử dụng đường dẫn `~/assets/`.

### Ví Dụ

Ví dụ, tham chiếu một file image sẽ được xử lý nếu công cụ build được cấu hình để xử lý extension file này:

```vue [app/app.vue]
<template>
  <img
    src="~/assets/img/nuxt.png"
    alt="Discover Nuxt"
  >
</template>
```

Nuxt sẽ không phục vụ các file trong thư mục `app/assets/` tại một static URL như `/assets/my-file.png`. Nếu bạn cần một static URL, hãy sử dụng thư mục `public/`.

## Static vs Dynamic `src`

Khi một `src` là một string literal tĩnh trong template của bạn, công cụ build sẽ viết lại nó thành một helper runtime để resolve URL cuối cùng. Một public path như `/img/nuxt.png` được bọc để `app.baseURL` được áp dụng khi page render, và một bundled path như `~/assets/img/nuxt.png` thêm vào trở thành một import resolve đến file output đã được hash.

```vue
<template>
  <!-- Các đường dẫn tĩnh được viết lại: app.baseURL được áp dụng tại runtime, và file bundled được hash. -->
  <img src="/img/nuxt.png">
  <img src="~/assets/img/nuxt.png">
</template>
```

Bởi vì `app.baseURL` được áp dụng tại runtime, một public path tĩnh hoạt động ngay cả khi base URL chỉ được biết tại thời điểm deploy (ví dụ được đặt qua `NUXT_APP_BASE_URL`), và nó hoạt động bất kể file có được công cụ build xử lý hay không. Resolution này chỉ xảy ra cho các literal paths mà công cụ build có thể thấy.

Một `:src` được bind mà giá trị được assemble tại runtime là opaque đối với công cụ build, vì vậy không có việc viết lại nào xảy ra. String được sử dụng chính xác như được viết:

```vue
<template>
  <!-- Điều này không hoạt động: đường dẫn được build tại runtime, vì vậy Vite không bao giờ thấy nó như một import. -->
  <img :src="`~/assets/img/${name}.png`">
</template>
```

Một public path được build tại runtime như `/img/${name}.png` do đó **không** được prefix với `app.baseURL`. Nếu ứng dụng của bạn được deploy bên dưới origin root, hãy tự prefix nó với `useRuntimeConfig().app.baseURL` (ví dụ qua `joinURL`).

Các phần dưới đây trình bày cách xử lý từng trường hợp khi đường dẫn chỉ được biết tại runtime.

### Public Assets

Nếu các file không cần được xử lý hoặc hash, hãy đặt chúng trong thư mục `public/` và tham chiếu chúng bằng URL:

```vue [app/app.vue]
<script setup lang="ts">
const props = defineProps<{
  name: string
}>()

const imageUrl = computed(() => `/img/${props.name}.png`)
</script>

<template>
  <img
    :src="imageUrl"
    :alt="props.name"
  >
</template>
```

Các file trong `public/` giữ nguyên tên file gốc của chúng.

### Bundled Assets với Vite

Các cách tiếp cận dưới đây là cụ thể cho Vite, builder mặc định của Nuxt.

Khi các file có thể có được biết trước, liệt kê các imports của chúng một cách tường minh:

```vue [app/app.vue]
<script setup lang="ts">
const props = defineProps<{
  theme: 'light' | 'dark'
}>()

const logos = {
  light: () => import('./assets/img/logo-light.png?url'),
  dark: () => import('./assets/img/logo-dark.png?url'),
}

const logoUrl = (await logos[props.theme]()).default
</script>

<template>
  <img
    :src="logoUrl"
    alt="Nuxt"
  >
</template>
```

Mỗi import có một literal path, vì vậy Vite có thể tìm cả hai file tại thời điểm build trong khi chỉ load module được chọn tại runtime.

Khi nhiều file chia sẻ một thư mục và extension, hãy sử dụng một biến dynamic import thay vì liệt kê mọi file:

```ts
async function getImageUrl (name: string) {
  const image = await import(`./assets/img/${name}.png?url`)
  return image.default
}
```

Chỉ filename mới có thể là dynamic trong ví dụ này. Giữ directory và extension trong import cho phép Vite tìm các file có thể có tại thời điểm build.

Đối với một pattern rộng hơn hoặc một map rõ ràng của các file có sẵn, hãy sử dụng `import.meta.glob`:

```ts
const images = import.meta.glob<string>('./assets/img/*.{png,jpg,svg}', {
  query: '?url',
  import: 'default',
})

async function getImageUrl (name: string) {
  const load = images[`./assets/img/${name}.png`]

  if (!load) {
    throw new Error(`Unknown image: ${name}`)
  }

  return await load()
}
```

Glob imports được lazy theo mặc định. Thêm `eager: true` nếu các URL phải có sẵn đồng bộ:

```ts
const images = import.meta.glob<string>('./assets/img/*.{png,jpg,svg}', {
  query: '?url',
  import: 'default',
  eager: true,
})
```

Mọi asset matching vẫn được include trong build output. Lazy imports load từng match theo yêu cầu, trong khi một eager glob load tất cả các matches lên trước và có thể tăng kích thước JavaScript ban đầu hoặc inline các assets nhỏ.

`await` một lazy import trước khi sử dụng URL của nó trong server-rendered markup. Pattern `new URL(..., import.meta.url)` của Vite không hoạt động với SSR.

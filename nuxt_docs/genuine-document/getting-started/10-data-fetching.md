# Data Fetching

Nuxt cung cấp các composables để xử lý việc lấy dữ liệu trong ứng dụng của bạn.

Nuxt đi kèm với hai composables và một thư viện tích hợp để thực hiện data-fetching trong môi trường trình duyệt hoặc server: `useFetch`, `useAsyncData` và `$fetch`.

Tóm gọn:

- `$fetch` là cách đơn giản nhất để thực hiện một network request.
- `useFetch` là một wrapper xung quanh `$fetch` chỉ lấy dữ liệu một lần trong universal rendering.
- `useAsyncData` tương tự `useFetch` nhưng cung cấp kiểm soát chi tiết hơn.

Cả `useFetch` và `useAsyncData` chia sẻ một tập hợp chung các tùy chọn và patterns mà chúng ta sẽ trình bày chi tiết trong các phần cuối.

## Tại Sao Cần `useFetch` và `useAsyncData`

Nuxt là một framework có thể chạy code isomorphic (hoặc universal) trong cả môi trường server và client. Nếu function `$fetch` được sử dụng để thực hiện data fetching trong setup function của một Vue component, điều này có thể gây ra dữ liệu được fetch hai lần, một lần trên server (để render HTML) và một lần trên client (khi HTML được hydrate). Điều này có thể gây ra hydration issues, tăng thời gian interactivity và gây ra hành vi không thể đoán trước.

Các composables `useFetch` và `useAsyncData` giải quyết vấn đề này bằng cách đảm bảo rằng nếu một API call được thực hiện trên server, dữ liệu được forward đến client trong payload.

Payload là một JavaScript object có thể truy cập qua `useNuxtApp().payload`. Nó được sử dụng trên client để tránh refetch cùng dữ liệu khi code được thực thi trong trình duyệt trong quá trình hydration.

```vue
<script setup lang="ts">
const { data } = await useFetch('/api/data')

async function handleFormSubmit () {
  const res = await $fetch('/api/submit', {
    method: 'POST',
    body: {
      // My form data
    },
  })
}
</script>

<template>
  <div v-if="data == undefined">
    No data
  </div>
  <div v-else>
    <form @submit="handleFormSubmit">
      <!-- form input tags -->
    </form>
  </div>
</template>
```

### Suspense

Nuxt sử dụng component `<Suspense>` của Vue bên dưới để ngăn navigation trước khi mọi async data có sẵn cho view. Các composables data fetching có thể giúp bạn tận dụng tính năng này và sử dụng những gì phù hợp nhất trên cơ sở per-call.

### Một ghi chú về `await`

Các ví dụ trong tài liệu này thường `await` các calls đến `useFetch` và `useAsyncData`, nhưng điều này không phải lúc nào cũng bắt buộc.

`await` **không** thay đổi server-rendered HTML. Trong server rendering, Nuxt đợi request resolve trước khi serialize page (dù có `<Suspense>`, và `onServerPrefetch` bên dưới), vì vậy kết quả được populate đầy đủ luôn được gửi đến trình duyệt.

Điều `await` **thay đổi** là điều xảy ra tiếp theo trong `<script setup>` của bạn, và cách client-side navigation hoạt động:

- **Với await**, execution tạm dừng cho đến khi data sẵn sàng, vì vậy bất kỳ code nào sau call có thể dựa vào `data` đã được populate. Trong client-side navigation, điều này block navigation cho đến khi request resolve.
- **Không có await**, execution tiếp tục ngay lập tức trong khi request chạy ở background, vì vậy `data` bắt đầu là giá trị mặc định và điền vào sau khi request resolve.

## `$fetch`

Nuxt bao gồm thư viện ofetch, và được auto-imported như alias `$fetch` toàn cục trên ứng dụng.

```vue
<script setup lang="ts">
async function addTodo () {
  const todo = await $fetch('/api/todos', {
    method: 'POST',
    body: {
      // My todo data
    },
  })
}
</script>
```

:::warning
Sử dụng chỉ `$fetch` sẽ không cung cấp network call de-duplication và navigation prevention.
:::

### Truyền Client Headers Đến API

Khi gọi `useFetch` trên server, Nuxt sẽ sử dụng `useRequestFetch` để proxy các client headers và cookies.

```vue
<script setup lang="ts">
const headers = useRequestHeaders(['cookie'])

async function getCurrentUser () {
  return await $fetch('/api/me', { headers })
}
</script>
```

## `useFetch`

Composable `useFetch` sử dụng `$fetch` bên dưới để thực hiện các SSR-safe network calls trong setup function.

```vue
<script setup lang="ts">
const { data: count } = await useFetch('/api/count')
</script>

<template>
  <p>Page visits: {{ count }}</p>
</template>
```

## `useAsyncData`

Composable `useAsyncData` chịu trách nhiệm wrap async logic và trả về kết quả một khi nó được resolve.

`useFetch(url)` gần như tương đương với `useAsyncData(url, () => event.$fetch(url))`.

```vue
<script setup lang="ts">
const { data, error } = await useAsyncData('users', () => myGetFunction('users'))
</script>
```

Argument đầu tiên của `useAsyncData` là một unique key được sử dụng để cache response của argument thứ hai, querying function.

## Return Values

`useFetch` và `useAsyncData` có cùng các return values:

- `data`: kết quả của async function được truyền vào.
- `refresh`/`execute`: một function có thể được sử dụng để refresh data.
- `clear`: một function có thể được sử dụng để set `data` thành `undefined`.
- `error`: một error object nếu data fetching thất bại.
- `status`: một string cho biết trạng thái của data request (`"idle"`, `"pending"`, `"success"`, `"error"`).

## Tùy Chọn

### Lazy

Theo mặc định, các data fetching composables sẽ đợi resolution của async function trước khi navigate đến một page mới. Sử dụng tùy chọn `lazy` để bỏ qua tính năng này trong client-side navigation.

```vue
<script setup lang="ts">
const { status, data: posts } = useFetch('/api/posts', {
  lazy: true,
})
</script>
```

### Client-only fetching

Đặt tùy chọn `server` thành `false` để chỉ thực hiện call trên client-side.

```ts
const { status, data: comments } = useFetch('/api/comments', {
  lazy: true,
  server: false,
})
```

### Giảm thiểu Payload Size

Tùy chọn `pick` giúp bạn giảm payload size bằng cách chỉ chọn các fields bạn muốn trả về.

```vue
<script setup lang="ts">
const { data: mountain } = await useFetch('/api/mountains/everest', {
  pick: ['title', 'description'],
})
</script>
```

### Caching và Refetching

`useFetch` và `useAsyncData` sử dụng keys để ngăn refetch cùng data.

```ts
// Sử dụng một computed property làm key
const userId = ref('123')
const { data: user } = useAsyncData(
  computed(() => `user-${userId.value}`),
  () => fetchUser(userId.value),
)
```

### Computed URL

Đôi khi bạn có thể cần compute một URL từ các reactive values. Thay vì xoay xở, bạn có thể đính kèm mỗi param như một reactive value.

```vue
<script setup lang="ts">
const id = ref(null)

const { data, status } = useLazyFetch('/api/user', {
  query: {
    user_id: id,
  },
})
</script>
```

### Not Immediate

Đặt `immediate: false` để ngăn việc bắt đầu fetch cho đến khi có tương tác của người dùng.

```vue
<script setup lang="ts">
const { data, error, execute, status } = await useLazyFetch('/api/comments', {
  immediate: false,
})
</script>
```

## Options API Support

Nuxt cung cấp một cách để thực hiện `asyncData` fetching trong Options API. Bạn phải wrap component definition trong `defineNuxtComponent`.

```vue
<script>
export default defineNuxtComponent({
  fetchKey: 'hello',
  async asyncData () {
    return {
      hello: await $fetch('/api/hello'),
    }
  },
})
</script>
```

## Serializing Data

Khi sử dụng `useAsyncData` và `useLazyAsyncData` để transfer data được fetch trên server đến client, payload được serialize với `devalue`. Điều này cho phép chúng ta transfer không chỉ basic JSON mà còn serialize và deserialize các loại data nâng cao hơn như regular expressions, Dates, Map và Set, `ref`, `reactive`, `shallowRef`, `shallowReactive` và `NuxtError`.

## Recipes

### Consuming SSE (Server-Sent Events)

```ts
const response = await $fetch<ReadableStream>('/chats/ask-ai', {
  method: 'POST',
  body: {
    query: 'Hello AI, how are you?',
  },
  responseType: 'stream',
})

const reader = response.pipeThrough(new TextDecoderStream()).getReader()

while (true) {
  const { value, done } = await reader.read()

  if (done) { break }

  console.log('Received:', value)
}
```

### Making Parallel Requests

```ts
const { data } = await useAsyncData((_nuxtApp, { signal }) => {
  return Promise.all([
    $fetch('/api/comments/', { signal }),
    $fetch('/api/author/12', { signal }),
  ])
})
```

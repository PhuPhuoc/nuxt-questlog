# Utils

## $fetch

### Mô Tả

Nuxt sử dụng ofetch để expose globally helper `$fetch` để thực hiện HTTP requests.

Nuxt sử dụng ofetch để expose globally helper `$fetch` để thực hiện HTTP requests trong Vue app hoặc API routes của bạn.

Trong server-side rendering, gọi `$fetch` để fetch các internal API routes sẽ trực tiếp gọi function tương ứng (mô phỏng request), tiết kiệm một API call bổ sung.

### Cách Sử Dụng

Sử dụng `$fetch` trong các methods chỉ được thực thi phía client.

```ts
async function addTodo () {
  const todo = await $fetch('/api/todos', {
    method: 'POST',
    body: {
      title: 'New Todo'
    },
  })
}
```

### Truyền Headers và Cookies

Khi gọi `$fetch` trong trình duyệt, các user headers như cookie sẽ được gửi trực tiếp đến API.

Tuy nhiên, trong Server-Side Rendering, vì lý do bảo mật như Server-Side Request Forgery (SSRF) hoặc Authentication Misuse, `$fetch` sẽ không bao gồm cookies của người dùng trình duyệt.

Nếu bạn cần forward headers và cookies trên server, bạn phải truyền chúng thủ công.

```ts
const headers = useRequestHeaders(['cookie'])

async function getCurrentUser () {
  return await $fetch('/api/me', { headers })
}
```

### Lưu Ý Quan Trọng

Sử dụng chỉ `$fetch` mà không wrap trong `useAsyncData` gây ra fetching data hai lần: ban đầu trên server, sau đó lại trên client-side trong quá trình hydration, vì `$fetch` không transfer state từ server đến client.

### Self-Signed Certificates

Nếu bạn sử dụng `$fetch` để gọi một HTTPS URL với self-signed certificate trong development, bạn cần đặt `NODE_TLS_REJECT_UNAUTHORIZED=0` trong environment của bạn.

# Navigation - Điều Hướng

> **Mục tiêu:** Cách navigate giữa các pages.

## NuxtLink

```vue
<NuxtLink to="/about">About</NuxtLink>
<NuxtLink :to="`/blog/${slug}`">Post</NuxtLink>
```

## Programmatic Navigation

```typescript
// useRouter
const router = useRouter()

router.push('/about')
router.push({ path: '/blog', query: { page: 1 } })
router.back()
```

## navigateTo

```typescript
await navigateTo('/about')
await navigateTo({ path: '/blog', query: { page: 1 } })
```

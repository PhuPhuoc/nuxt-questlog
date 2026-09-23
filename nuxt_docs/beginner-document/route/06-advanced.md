# Advanced Routing - Routing Nâng Cao

> **Mục tiêu:** Các kỹ thuật routing nâng cao.

## Dynamic Routes

### Required Segment

```
[slug].vue → /:slug
```

```typescript
const route = useRoute()
const slug = route.params.slug
```

### Optional Segment

```
[[slug]].vue → / hoặc /:slug
```

### Catch-all

```
[...slug].vue → /*
```

## Route Meta

```vue
<script setup lang="ts">
definePageMeta({
  meta: {
    title: 'About Page',
    requiresAuth: true
  }
})
</script>
```

## Page Transitions

```vue
<template>
  <NuxtPage :transition="{ name: 'fade' }" />
</template>
```

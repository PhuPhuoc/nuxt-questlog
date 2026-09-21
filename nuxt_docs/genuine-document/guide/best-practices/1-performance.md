# Best Practices

## Nuxt Performance

## Built-in Features

Nuxt cung cấp một số tính năng built-in giúp tối ưu performance của website.

### Links

`NuxtLink` là drop-in replacement cho cả `RouterLink` của Vue Router và tag `<a>` của HTML.

```html
<template>
  <NuxtLink to="/about">About page</NuxtLink>
</template>
```

Nuxt tự động bao gồm smart prefetching.

### Hybrid Rendering

```ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },
    '/products/**': { swr: 3600 },
    '/blog/**': { isr: 3600 },
    '/admin/**': { ssr: false },
  },
})
```

### Lazy Loading Components

```html
<script setup lang="ts">
const show = ref(false)
</script>

<template>
  <div>
    <h1>Mountains</h1>
    <LazyMountainsList v-if="show" />
    <button v-if="!show" @click="show = true">Show List</button>
  </div>
</template>
```

### Lazy Hydration

```html
<template>
  <div>
    <LazyMyComponent hydrate-on-visible />
  </div>
</template>
```

## Core Nuxt Modules

### Images

Module Nuxt Image cung cấp image optimization cho Nuxt apps.

```html
<template>
  <NuxtImg
    src="/hero-banner.jpg"
    format="webp"
    :preload="{ fetchPriority: 'high' }"
    loading="eager"
    width="200"
    height="100"
  />
</template>
```

### Fonts

Nuxt Fonts tự động optimize fonts và loại bỏ external network requests.

### Scripts

Nuxt Scripts cho phép load third-party scripts với performance tốt hơn.

```ts
const { onLoaded, proxy } = useScriptGoogleAnalytics({
  id: 'G-1234567',
  scriptOptions: {
    trigger: 'manual',
  },
})
```

## Profiling Tools

### Nuxi Analyze

```bash
npx nuxi analyze
```

### Nuxt DevTools

Nuxt DevTools cung cấp insights về Nuxt App để identify performance gaps.

Tính năng:
1. Timeline - Theo dõi thời gian render, update và initialize components
2. Assets - Hiển thị file sizes
3. Render Tree - Hiển thị connections giữa Vue components, scripts và styles
4. Inspect - Liệt kê tất cả files với size và evaluation time

### Chrome DevTools

Performance panel hiển thị LCP và CLS scores ngay lập tức.

Lighthouse audits performance, accessibility, SEO và progressive web apps.

### PageSpeed Insights

PSI báo cáo trải nghiệm người dùng trên cả mobile và desktop.

## Common Problems

### Overusing Plugins
Plugins số lượng lớn có thể gây performance issues, đặc biệt nếu chúng yêu cầu computations đắt hoặc mất quá lâu để initialize.

**Giải pháp:** Kiểm tra plugins và xem liệu một số có thể được implement như composable hoặc utility function thay thế.

### Unused Code / Dependencies
**Giải pháp:** Kiểm tra package.json cho unused dependencies và analyze code cho unused utils/composables/functions.

### Not Using Vue Performance Tips
**Giải pháp:** Sử dụng các concepts như shallowRef, v-memo, v-once để cải thiện performance.

### Not Following Patterns
**Giải pháp:** Thiết lập rules và patterns trong project.

### Trying to Load Everything at the Same Time
**Giải pháp:** Sử dụng Progressive Enhancement where core webpage content được set first.

# Custom Routing

Trong Nuxt, routing được định nghĩa bởi cấu trúc của các files bên trong thư mục pages. Tuy nhiên, vì nó sử dụng vue-router bên dưới, Nuxt cung cấp nhiều cách để thêm custom routes trong project.

## Thêm Custom Routes

### Router Config

Sử dụng router options, bạn có thể override hoặc extend routes sử dụng một function.

```ts [router.options.ts]
import type { RouterConfig } from '@nuxt/schema'

export default {
  routes: _routes => [
    {
      name: 'home',
      path: '/',
      component: () => import('~/pages/home.vue'),
    },
  ],
} satisfies RouterConfig
```

### Pages Hook

```ts [nuxt.config.ts]
export default defineNuxtConfig({
  hooks: {
    'pages:extend' (pages) {
      pages.push({
        name: 'profile',
        path: '/profile',
        file: '~/extra-pages/profile.vue',
      })
    },
  },
})
```

### Nuxt Module

Nuxt kit cung cấp các cách để thêm routes:
- `extendPages` (callback: pages => void)
- `extendRouteRules` (route: string, rule: NitroRouteConfig, options: ExtendRouteRulesOptions)

## Router Options

### Sử dụng `router.options`

```ts [app/router.options.ts]
import type { RouterConfig } from '@nuxt/schema'

export default {
} satisfies RouterConfig
```

### Sử dụng `nuxt.config`

```ts
export default defineNuxtConfig({
  router: {
    options: {},
  },
})
```

Các options có thể cấu hình:
- `linkActiveClass`
- `linkExactActiveClass`
- `end`
- `sensitive`
- `strict`
- `hashMode`
- `scrollBehaviorType`

### Hash Mode (SPA)

```ts
export default defineNuxtConfig({
  ssr: false,
  router: {
    options: {
      hashMode: true,
    },
  },
})
```

### Scroll Behavior

```ts
export default defineNuxtConfig({
  router: {
    options: {
      scrollBehaviorType: 'smooth',
    },
  },
})
```

### Custom History

```ts
import { createMemoryHistory } from 'vue-router'

export default {
  history: base => import.meta.client ? createMemoryHistory(base) : null,
} satisfies RouterConfig
```

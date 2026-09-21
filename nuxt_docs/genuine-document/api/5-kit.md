# Nuxt Kit

Nuxt Kit cung cấp một tập hợp các utilities để giúp bạn tạo và sử dụng modules.

## `defineNuxtModule`

### Mô Tả

Định nghĩa một Nuxt module, tự động merge defaults với các options được cung cấp bởi user, cài đặt bất kỳ hooks nào được cung cấp, và gọi một setup function tùy chọn để kiểm soát hoàn toàn.

### Cách Sử Dụng

```ts
import { defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  meta: {
    name: 'my-module',
    configKey: 'myModule',
  },
  defaults: {
    enabled: true,
  },
  setup (options) {
    if (options.enabled) {
      console.log('My Nuxt module is enabled!')
    }
  },
})
```

### Parameters

**definition**: Một module definition object hoặc module function.

- **meta**: Metadata của module. Định nghĩa tên module, version, config key và compatibility.
- **defaults**: Default options cho module.
- **schema**: Schema cho module options.
- **hooks**: Hooks được cài đặt cho module.
- **moduleDependencies**: Dependencies trên các modules khác.
- **onInstall**: Lifecycle hook được gọi khi module được cài đặt lần đầu.
- **onUpgrade**: Lifecycle hook được gọi khi module được upgrade lên phiên bản mới hơn.
- **setup**: Setup function cho module.

### Ví Dụ

#### Sử Dụng `configKey` để Làm Module Có Thể Cấu Hình

```ts
export default defineNuxtModule({
  meta: {
    name: 'my-module',
    configKey: 'myModule',
  },
  defaults: {
    enabled: true,
  },
  setup (options) {
    if (options.enabled) {
      console.log('My Nuxt module is enabled!')
    }
  },
})
```

Users có thể cung cấp options trong `nuxt.config`:

```ts
export default defineNuxtConfig({
  myModule: {
    enabled: false,
  },
})
```

#### Định Nghĩa Module Compatibility Requirements

```ts
export default defineNuxtModule({
  meta: {
    name: '@nuxt/icon',
    configKey: 'icon',
    compatibility: {
      nuxt: '>=3.0.0',
    },
  },
  setup () {
    const resolver = createResolver(import.meta.url)
  },
})
```

#### Type Safety với `.with()`

```ts
interface ModuleOptions {
  apiKey: string
  baseURL: string
  timeout?: number
  retries?: number
}

export default defineNuxtModule<ModuleOptions>().with({
  meta: {
    name: '@nuxtjs/my-api',
    configKey: 'myApi',
  },
  defaults: {
    baseURL: 'https://api.example.com',
    timeout: 5000,
    retries: 3,
  },
  setup (resolvedOptions, nuxt) {
    console.log(resolvedOptions.baseURL)
    console.log(resolvedOptions.timeout)
  },
})
```

#### Sử Dụng Lifecycle Hooks

```ts
export default defineNuxtModule({
  meta: {
    name: 'my-awesome-module',
    version: '1.2.0',
    configKey: 'myAwesomeModule',
  },
  defaults: {
    apiKey: '',
    enabled: true,
  },

  onInstall (nuxt) {
    console.log('Setting up my-awesome-module for the first time!')
  },

  onUpgrade (nuxt, options, previousVersion) {
    console.log(`Upgrading my-awesome-module from ${previousVersion} to 1.2.0`)
  },

  setup (options, nuxt) {
    if (options.enabled) {
      // Configure the module
    }
  },
})
```

#### Chỉ Định Module Dependencies

```ts
export default defineNuxtModule({
  meta: {
    name: 'my-module',
  },
  moduleDependencies: {
    '@nuxtjs/tailwindcss': {
      version: '>=6.0.0',
      overrides: {
        exposeConfig: true,
      },
      defaults: {
        config: {
          darkMode: 'class',
        },
      },
    },
    '@nuxtjs/fontaine': {
      optional: true,
      defaults: {
        fonts: [
          {
            family: 'Roboto',
            fallbacks: ['Impact'],
          },
        ],
      },
    },
  },
  setup (options, nuxt) {

  },
})
```

## `installModule`

:::warning
**Deprecated:** Sử dụng tùy chọn `moduleDependencies` trong `defineNuxtModule` thay thế.
:::

### Mô Tả

Cài đặt một Nuxt module theo chương trình.

### Cách Sử Dụng

```ts
import { defineNuxtModule, installModule } from '@nuxt/kit'

export default defineNuxtModule({
  async setup () {
    await installModule('@nuxtjs/fontaine', {
      fonts: [
        {
          family: 'Roboto',
          fallbacks: ['Impact'],
          fallbackName: 'fallback-a',
        },
      ],
    })
  },
})
```

### Type

```ts
async function installModule (
  moduleToInstall: string | NuxtModule,
  inlineOptions?: any,
  nuxt?: Nuxt
)
```

### Parameters

- **moduleToInstall**: Module để cài đặt. Có thể là string với module name hoặc một module object.
- **inlineOptions**: Object với module options được truyền đến module's setup function.
- **nuxt**: Nuxt instance.

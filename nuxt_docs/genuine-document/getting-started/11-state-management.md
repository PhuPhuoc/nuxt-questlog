# Quản Lý State

Nuxt cung cấp các thư viện quản lý state mạnh mẽ và composable `useState` để tạo một reactive và SSR-friendly shared state.

Nuxt cung cấp composable `useState` để tạo một reactive và SSR-friendly shared state xuyên suốt các components.

`useState` là một thay thế cho `ref` thân thiện với SSR. Giá trị của nó sẽ được preserve sau server-side rendering (trong quá trình client-side hydration) và được chia sẻ qua tất cả các components sử dụng một unique key.

## Lưu Ý Quan Trọng

Bởi vì dữ liệu bên trong `useState` sẽ được serialize thành JSON, điều quan trọng là nó không chứa bất kỳ thứ gì không thể serialize, như classes, functions hoặc symbols.

## Best Practices

Không bao giờ định nghĩa `const state = ref()` bên ngoài `<script setup>` hoặc function `setup()`. Ví dụ, làm `export myState = ref({})` sẽ dẫn đến state được chia sẻ qua các requests trên server và có thể dẫn đến memory leaks.

Thay vào đó hãy sử dụng `const useX = () => useState('x')`

## Ví Dụ

### Basic Usage

Trong ví dụ này, chúng ta sử dụng một component-local counter state. Bất kỳ component nào khác sử dụng `useState('counter')` sẽ chia sẻ cùng một reactive state.

```vue [app/app.vue]
<script setup lang="ts">
const counter = useState('counter', () => Math.round(Math.random() * 1000))
</script>

<template>
  <div>
    Counter: {{ counter }}
    <button @click="counter++">
      +
    </button>
    <button @click="counter--">
      -
    </button>
  </div>
</template>
```

### Initializing State

Hầu hết thời gian, bạn sẽ muốn khởi tạo state với dữ liệu resolve một cách bất đồng bộ. Bạn có thể sử dụng component `app.vue` với tiện ích `callOnce` để làm điều này.

```vue [app/app.vue]
<script setup lang="ts">
const websiteConfig = useState('config')

await callOnce(async () => {
  websiteConfig.value = await $fetch('https://my-cms.com/api/website-config')
})
</script>
```

Điều này tương tự với action `nuxtServerInit` trong Nuxt 2, cho phép điền initial state của store phía server trước khi render page.

### Sử Dụng với Pinia

Trong ví dụ này, chúng ta tận dụng Pinia module để tạo một global store và sử dụng nó xuyên suốt app.

Đảm bảo cài đặt Pinia module với `npx nuxt module add pinia` hoặc làm theo các bước cài đặt của module.

```ts [app/stores/website.ts]
export const useWebsiteStore = defineStore('websiteStore', {
  state: () => ({
    name: '',
    description: '',
  }),
  actions: {
    async fetch () {
      const infos = await $fetch('https://api.nuxt.com/modules/pinia')

      this.name = infos.name
      this.description = infos.description
    },
  },
})
```

```vue [app/app.vue]
<script setup lang="ts">
const website = useWebsiteStore()

await callOnce(website.fetch)
</script>

<template>
  <main>
    <h1>{{ website.name }}</h1>
    <p>{{ website.description }}</p>
  </main>
</template>
```

## Advanced Usage

```ts [app/composables/locale.ts]
import type { Ref } from 'vue'

export const useLocale = () => {
  return useState<string>('locale', () => useDefaultLocale().value)
}

export const useDefaultLocale = (fallback = 'en-US') => {
  const locale = ref(fallback)
  if (import.meta.server) {
    const reqLocale = useRequestHeaders()['accept-language']?.split(',')[0]
    if (reqLocale) {
      locale.value = reqLocale
    }
  } else if (import.meta.client) {
    const navLang = navigator.language
    if (navLang) {
      locale.value = navLang
    }
  }
  return locale
}

export const useLocales = () => {
  const locale = useLocale()
  const locales = ref([
    'en-US',
    'en-GB',
    'ja-JP-u-ca-japanese',
  ])
  if (!locales.value.includes(locale.value)) {
    locales.value.unshift(locale.value)
  }
  return locales
}

export const useLocaleDate = (date: Ref<Date> | Date, locale = useLocale()) => {
  return computed(() => new Intl.DateTimeFormat(locale.value, { dateStyle: 'full' }).format(unref(date)))
}
```

```vue [app/app.vue]
<script setup lang="ts">
const locales = useLocales()
const locale = useLocale()
const date = useLocaleDate(new Date('2016-10-26'))
</script>

<template>
  <div>
    <h1>Nuxt birthday</h1>
    <p>{{ date }}</p>
    <label for="locale-chooser">Preview a different locale</label>
    <select
      id="locale-chooser"
      v-model="locale"
    >
      <option
        v-for="loc of locales"
        :key="loc"
        :value="loc"
      >
        {{ loc }}
      </option>
    </select>
  </div>
</template>
```

## Shared State

Bằng cách sử dụng các composables được auto-imported, chúng ta có thể định nghĩa các global type-safe states và import chúng xuyên suốt app.

```ts [composables/states.ts]
export const useColor = () => useState<string>('color', () => 'pink')
```

```vue [app/app.vue]
<script setup lang="ts">
const useColor = () => useState<string>('color', () => 'pink')
const color = useColor() // Same as useState('color')
</script>

<template>
  <p>Current color: {{ color }}</p>
</template>
```

## Sử Dụng Các Thư Viện Bên Thứ Ba

Nuxt từng dựa vào thư viện Vuex để cung cấp global state management. Nếu bạn đang migrate từ Nuxt 2, vui lòng tham khảo migration guide.

Nuxt không bị bound về state management, vì vậy hãy thoải mái chọn giải pháp phù hợp với nhu cầu của bạn. Có nhiều integrations với các thư viện state management phổ biến nhất, bao gồm:

- **Pinia** - đề xuất chính thức của Vue
- **Harlem** - immutable global state management
- **XState** - state machine approach với các công cụ để visualize và test state logic

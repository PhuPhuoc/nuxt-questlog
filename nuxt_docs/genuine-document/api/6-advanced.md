# Advanced

## Lifecycle Hooks

Nuxt cung cấp một hệ thống hooking mạnh mẽ để mở rộng hầu như mọi khía cạnh sử dụng hooks.

## App Hooks (Runtime)

| Hook | Arguments | Environment | Description |
|------|-----------|-------------|-------------|
| `app:created` | vueApp | Server & Client | Được gọi khi vueApp instance ban đầu được tạo |
| `app:error` | err | Server & Client | Được gọi khi xảy ra fatal error |
| `app:error:cleared` | { redirect? } | Server & Client | Được gọi khi fatal error được xóa |
| `vue:setup` | - | Server & Client | Được gọi khi setup của Nuxt root được khởi tạo |
| `vue:error` | err, target, info | Server & Client | Được gọi khi vue error lan truyền đến root component |
| `app:rendered` | renderContext | Server | Được gọi khi SSR rendering hoàn thành |
| `app:redirected` | - | Server | Được gọi trước SSR redirection |
| `app:beforeMount` | vueApp | Client | Được gọi trước khi mount app, chỉ phía client |
| `app:mounted` | vueApp | Client | Được gọi khi Vue app được khởi tạo và mount trong browser |
| `app:suspense:resolve` | appComponent | Client | On Suspense resolved event |
| `app:manifest:update` | { id, timestamp } | Client | Được gọi khi có phiên bản mới của app được phát hiện |
| `app:data:refresh` | keys? | Client | Được gọi khi refreshNuxtData được gọi |
| `link:prefetch` | to | Client | Được gọi khi NuxtLink được quan sát để prefetch |
| `page:start` | pageComponent? | Client | Được gọi khi Suspense bên trong NuxtPage pending |
| `page:finish` | pageComponent? | Client | Được gọi khi Suspense bên trong NuxtPage resolved |
| `page:loading:start` | - | Client | Được gọi khi route navigation bắt đầu |
| `page:loading:end` | - | Client | Được gọi sau page:finish |
| `page:transition:finish` | pageComponent? | Client | Sau page transition onAfterLeave event |
| `dev:ssr-logs` | logs | Client | Được gọi với một mảng các server-side logs |
| `page:view-transition:start` | transition | Client | Được gọi khi document.startViewTransition được gọi |

## Nuxt Hooks (Build Time)

| Hook | Arguments | Description |
|------|-----------|-------------|
| `kit:compatibility` | compatibility, issues | Cho phép mở rộng compatibility checks |
| `ready` | nuxt | Được gọi sau khi Nuxt khởi tạo xong |
| `close` | nuxt | Được gọi khi Nuxt instance đóng một cách graceful |
| `restart` | { hard?: boolean } | Được gọi để restart Nuxt instance |
| `modules:before` | - | Được gọi trước khi cài đặt user modules |
| `modules:done` | - | Được gọi sau khi cài đặt user modules xong |
| `module:before` | module | Được gọi trước khi mỗi module được setup |
| `module:done` | module | Được gọi sau khi mỗi module được setup xong |
| `app:resolve` | app | Được gọi sau khi resolve app instance |
| `app:templates` | app | Được gọi trong quá trình NuxtApp generation |
| `app:templatesGenerated` | app | Được gọi sau khi templates được compile vào vfs |
| `build:before` | - | Được gọi trước khi Nuxt bundle builder |
| `build:done` | - | Được gọi sau khi Nuxt bundle builder hoàn thành |
| `build:manifest` | manifest | Được gọi trong quá trình manifest build |
| `builder:generateApp` | options | Được gọi trước khi generate app |
| `builder:watch` | event, path | Được gọi khi watcher phát hiện thay đổi file |
| `pages:extend` | pages | Được gọi sau khi page routes được scan |
| `pages:resolved` | pages | Được gọi sau khi page routes được augment với metadata |
| `pages:routerOptions` | { files } | Được gọi khi resolve router.options files |
| `server:routes` | routes, context | Được gọi khi Nuxt cần full set route handlers |
| `server:devHandler` | handler | Được gọi khi dev middleware được đăng ký |
| `imports:sources` | presets | Cho phép modules mở rộng sources |
| `imports:extend` | imports | Cho phép mở rộng imports |
| `imports:context` | context | Được gọi khi unimport context được tạo |
| `imports:dirs` | dirs | Cho phép mở rộng import directories |
| `components:dirs` | dirs | Cho phép mở rộng directories cho components |
| `components:extend` | components | Cho phép mở rộng components mới |
| `nitro:config` | nitroConfig | Được gọi trước khi khởi tạo Nitro |
| `nitro:init` | nitro | Được gọi sau khi Nitro được khởi tạo |
| `nitro:build:before` | nitro | Được gọi trước khi build Nitro |
| `nitro:build:public-assets` | nitro | Được gọi sau khi copy public assets |
| `prerender:routes` | ctx | Cho phép mở rộng routes để pre-render |
| `build:error` | error | Được gọi khi xảy ra lỗi trong build time |
| `prepare:types` | options | Được gọi trước khi viết TypeScript config files |
| `listen` | listenerServer, listener | Được gọi khi dev server load |
| `schema:extend` | schemas | Cho phép mở rộng default schemas |
| `schema:resolved` | schema | Cho phép mở rộng resolved schema |
| `schema:beforeWrite` | schema | Được gọi trước khi viết schema |
| `schema:written` | - | Được gọi sau khi schema được viết |
| `vite:extend` | viteBuildContext | Cho phép mở rộng Vite default context |
| `vite:extendConfig` | viteInlineConfig, env | Cho phép mở rộng Vite default config |
| `vite:configResolved` | viteInlineConfig, env | Cho phép đọc resolved Vite config |
| `vite:serverCreated` | viteServer, env | Được gọi khi Vite server được tạo |
| `vite:compiled` | - | Được gọi sau khi Vite server được compile |
| `webpack:config` | webpackConfigs | Được gọi trước khi cấu hình webpack compiler |
| `webpack:configResolved` | webpackConfigs | Cho phép đọc resolved webpack config |
| `webpack:compile` | options | Được gọi ngay trước khi compilation |
| `webpack:compiled` | options | Được gọi sau khi resources được load |
| `webpack:change` | shortPath | Được gọi khi có thay đổi trên WebpackBar |
| `webpack:error` | - | Được gọi khi có lỗi trên WebpackBar |
| `webpack:done` | - | Được gọi trên allDone của WebpackBar |
| `webpack:progress` | statesArray | Được gọi trên progress của WebpackBar |

## Nitro App Hooks (Runtime, Server-Side)

| Hook | Arguments | Description |
|------|-----------|-------------|
| `dev:ssr-logs` | { path, logs } | Server | Được gọi cuối request cycle với server-side logs |
| `render:response` | response, { event } | Được gọi trước khi gửi response |
| `render:html` | html, { event } | Được gọi trước khi construct HTML |
| `render:island` | islandResponse, { event, islandContext } | Được gọi trước khi construct island HTML |
| `close` | - | Được gọi khi Nitro đóng |
| `error` | error, { event? } | Được gọi khi xảy ra lỗi |
| `request` | event | Được gọi khi nhận được request |
| `beforeResponse` | event, { body } | Được gọi trước khi gửi response |
| `afterResponse` | event, { body } | Được gọi sau khi gửi response |

# Giới Thiệu

Mục tiêu của Nuxt là làm cho việc phát triển web trở nên trực quan và hiệu quả với trải nghiệm nhà phát triển tuyệt vời.

Nuxt là một framework mã nguồn mở miễn phí với cách tiếp cận trực quan và có thể mở rộng để tạo ra các ứng dụng web full-stack và website có tính type-safe, hiệu năng cao và đạt chuẩn production với Vue.js.

Chúng tôi đã thiết kế mọi thứ để bạn có thể bắt đầu viết các file `.vue` ngay từ đầu, tận hưởng hot module replacement trong quá trình phát triển và ứng dụng có hiệu năng cao trong production với server-side rendering theo mặc định.

Nuxt không có vendor lock-in, cho phép bạn triển khai ứng dụng ở bất kỳ đâu, thậm chí trên edge.

:::tip
Nếu bạn muốn thử Nuxt ngay trong trình duyệt, bạn có thể trải nghiệm trong một trong các sandbox trực tuyến của chúng tôi.
:::

## Tự động hóa và Quy ước

Nuxt sử dụng các quy ước và cấu trúc thư mục có ý kiến để tự động hóa các tác vụ lặp đi lặp lại và cho phép nhà phát triển tập trung vào việc xây dựng tính năng. File cấu hình vẫn có thể tùy chỉnh và ghi đè các hành vi mặc định.

- **Routing dựa trên file**: định nghĩa các route dựa trên cấu trúc thư mục `app/pages/` của bạn. Điều này giúp tổ chức ứng dụng dễ dàng hơn và tránh cần cấu hình route thủ công.
- **Code splitting**: Nuxt tự động chia code thành các chunk nhỏ hơn, giúp giảm thời gian tải ban đầu của ứng dụng.
- **Server-side rendering có sẵn**: Nuxt đi kèm với khả năng SSR tích hợp sẵn, vì vậy bạn không cần thiết lập một server riêng biệt.
- **Auto-imports**: viết các composables và components của Vue trong các thư mục tương ứng và sử dụng chúng mà không cần import thủ công, với lợi ích từ tree-shaking và các bundle JS được tối ưu.
- **Tiện ích lấy dữ liệu**: Nuxt cung cấp các composables để xử lý việc lấy dữ liệu tương thích SSR cùng với các chiến lược khác nhau.
- **Hỗ trợ TypeScript không cần cấu hình**: viết code có tính type-safe mà không cần học TypeScript nhờ các type được tự động tạo và file tsconfig.json.
- **Công cụ build đã được cấu hình sẵn**: chúng tôi sử dụng Vite theo mặc định để hỗ trợ hot module replacement (HMR) trong phát triển và đóng gói code cho production với các best-practices tích hợp sẵn.

Nuxt chăm sóc tất cả những điều này và cung cấp cả chức năng frontend và backend để bạn có thể tập trung vào điều quan trọng: tạo ứng dụng web của mình.

## Server-Side Rendering

Nuxt đi kèm với khả năng server-side rendering (SSR) tích hợp sẵn theo mặc định, mà không cần cấu hình server riêng, điều này mang lại nhiều lợi ích cho các ứng dụng web:

- **Thời gian tải trang ban đầu nhanh hơn**: Nuxt gửi một trang HTML đã được render đầy đủ đến trình duyệt, có thể hiển thị ngay lập tức. Điều này có thể cung cấp thời gian tải trang được cảm nhận nhanh hơn và trải nghiệm người dùng tốt hơn (UX), đặc biệt trên các mạng hoặc thiết bị chậm.
- **SEO được cải thiện**: các công cụ tìm kiếm có thể lập chỉ mục các trang SSR tốt hơn vì nội dung HTML có sẵn ngay lập tức, thay vì cần JavaScript để render nội dung phía client.
- **Hiệu năng tốt hơn trên các thiết bị có cấu hình thấp**: giảm lượng JavaScript cần được tải xuống và thực thi phía client, điều này có lợi cho các thiết bị có cấu hình thấp có thể gặp khó khăn khi xử lý các ứng dụng JavaScript nặng.
- **Khả năng tiếp cận tốt hơn**: nội dung có sẵn ngay trên trang tải ban đầu, cải thiện khả năng tiếp cận cho người dùng phụ thuộc vào screen reader hoặc các công nghệ hỗ trợ khác.
- **Cache dễ dàng hơn**: các trang có thể được cache phía server, điều này có thể cải thiện hiệu năng hơn nữa bằng cách giảm thời gian tạo và gửi nội dung đến client.

Tổng thể, server-side rendering có thể cung cấp trải nghiệm người dùng nhanh hơn và hiệu quả hơn, cũng như cải thiện tối ưu hóa công cụ tìm kiếm và khả năng tiếp cận.

Vì Nuxt là một framework linh hoạt, nó cung cấp cho bạn khả năng render tĩnh toàn bộ ứng dụng đến một hosting tĩnh với `nuxt generate`, tắt SSR toàn cục với tùy chọn `ssr: false` hoặc tận dụng hybrid rendering bằng cách thiết lập tùy chọn routeRules.

## Server Engine

Server engine của Nuxt, Nitro, mở khóa các khả năng full-stack mới.

Trong phát triển, nó sử dụng Rollup và Node.js workers cho code phía server và cô lập context. Nó cũng tạo các server API của bạn bằng cách đọc các file từ `server/api/` và server middleware từ `server/middleware/`.

Trong production, Nitro build ứng dụng và server của bạn thành một thư mục `.output` duy nhất. Output này nhẹ: đã được minify và loại bỏ khỏi bất kỳ module Node.js nào (ngoại trừ polyfills). Bạn có thể deploy output này trên bất kỳ hệ thống nào hỗ trợ JavaScript, từ Node.js, Serverless, Workers, Edge-side rendering hoặc hoàn toàn tĩnh.

## Sẵn sàng cho Production

Ứng dụng Nuxt có thể được deploy trên server Node hoặc Deno, pre-rendered để host trong môi trường tĩnh, hoặc deploy lên các nhà cung cấp serverless và edge.

## Có tính Modular

Hệ thống module cho phép bạn mở rộng Nuxt với các tính năng tùy chỉnh và tích hợp với các dịch vụ bên thứ ba.

## Kiến trúc

Nuxt được cấu thành từ các core packages khác nhau:

- **Core engine**: nuxt
- **Bundlers**: @nuxt/vite-builder, @nuxt/rspack-builder và @nuxt/webpack-builder
- **Command line interface**: @nuxt/cli
- **Server engine**: nitro
- **Development kit**: @nuxt/kit

Chúng tôi khuyên bạn nên đọc các khái niệm (concepts) để có cái nhìn đầy đủ về khả năng của Nuxt và phạm vi của từng package.

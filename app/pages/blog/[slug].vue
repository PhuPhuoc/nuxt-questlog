<!-- app/pages/blog/[slug].vue -->
<script setup lang="ts">
// URL: /blog/:slug

const route = useRoute();
const slug = route.params.slug;

// Mock data
const posts = {
  "why-nuxt": {
    title: "Tại sao nên dùng Nuxt?",
    content: `
      <p>Nuxt là một framework mạnh mẽ xây dựng trên nền tảng Vue.js, cung cấp nhiều tính năng ưu việt:</p>

      <h3>1. Server-Side Rendering (SSR)</h3>
      <p>Nuxt hỗ trợ SSR sẵn có, giúp cải thiện SEO và tốc độ tải trang. Thay vì chờ JavaScript load xong mới hiển thị nội dung, server sẽ render HTML trước.</p>

      <h3>2. Auto-Imports</h3>
      <p>Không cần import Vue APIs như ref(), computed() mỗi lần sử dụng. Nuxt tự động nhận diện và import cho bạn.</p>

      <h3>3. File-Based Routing</h3>
      <p>Tạo route bằng cách tạo file. pages/index.vue = route /, pages/about.vue = route /about. Không cần cấu hình router!</p>

      <h3>4. Hybrid Rendering</h3>
      <p>Nuxt hỗ trợ nhiều rendering modes: SSR, SSG, SPA, ISR. Chọn mode phù hợp cho từng route.</p>
    `,
    category: "Nuxt",
    date: "15/01/2024",
    views: 100,
    author: "Nam",
  },
  "vue3-composition-api": {
    title: "Hướng dẫn Vue 3 Composition API",
    content: `
      <p>Composition API là cách viết code Vue 3 được khuyến nghị, thay thế Options API của Vue 2.</p>

      <h3>ref() và reactive()</h3>
      <p>Tạo reactive state với ref() cho primitive values và reactive() cho objects.</p>

      <h3>computed()</h3>
      <p>Tạo computed properties - tự động cập nhật khi dependencies thay đổi.</p>

      <h3>watch() và watchEffect()</h3>
      <p>Theo dõi sự thay đổi của reactive values và chạy side effects.</p>
    `,
    category: "Vue",
    date: "10/01/2024",
    views: 250,
    author: "Minh",
  },
  "typescript-with-nuxt": {
    title: "TypeScript với Nuxt cho người mới",
    content: `
      <p>TypeScript mang lại nhiều lợi ích cho việc phát triển ứng dụng Vue/Nuxt.</p>

      <h3>Lợi ích của TypeScript</h3>
      <ul>
        <li>Type safety - phát hiện lỗi sớm</li>
        <li>Autocomplete tốt hơn</li>
        <li>Refactoring dễ dàng hơn</li>
        <li>Documentation tự tạo</li>
      </ul>
    `,
    category: "TypeScript",
    date: "05/01/2024",
    views: 180,
    author: "Nam",
  },
};

// Lấy bài viết hiện tại
const post = computed(() => posts[slug as keyof typeof posts]);

// SEO - Cập nhật title theo bài viết
useSeoMeta({
  title: () =>
    post.value ? `${post.value.title} - BlogTalk` : "Không tìm thấy",
  description: () => post.value?.title || "",
});
</script>

<template>
  <div class="post-page">
    <!-- Breadcrumb -->
    <nav class="breadcrumb">
      <NuxtLink to="/">Trang chủ</NuxtLink>
      <span>/</span>
      <NuxtLink to="/blog">Blog</NuxtLink>
      <span>/</span>
      <span>{{ post?.title }}</span>
    </nav>

    <!-- Bài viết tồn tại -->
    <article v-if="post">
      <header class="post-header">
        <h1>{{ post.title }}</h1>

        <div class="post-meta">
          <span>{{ post.date }}</span>
          <span>{{ post.views }} lượt xem</span>
          <span>{{ post.author }}</span>
          <span class="category-badge">{{ post.category }}</span>
        </div>
      </header>

      <!-- Content -->
      <div class="post-content" v-html="post.content"></div>

      <!-- Back to blog -->
      <div class="back-link">
        <NuxtLink to="/blog">← Quay lại Blog</NuxtLink>
      </div>
    </article>

    <!-- Bài viết không tồn tại -->
    <div v-else class="not-found">
      <h1>404 - Bài viết không tồn tại</h1>
      <p>Bài viết bạn đang tìm kiếm không tồn tại.</p>
      <NuxtLink to="/blog">Quay lại Blog</NuxtLink>
    </div>
  </div>
</template>

<style scoped>
.post-page {
  max-width: 900px;
  margin: 0 auto;
  padding: var(--spacing-2xl) var(--spacing-xl);
}

/* Breadcrumb */
.breadcrumb {
  display: flex;
  gap: var(--spacing-sm);
  align-items: center;
  margin-bottom: var(--spacing-xl);
  font-size: var(--font-size-sm);
  color: var(--color-text-muted);
}

.breadcrumb a {
  color: var(--color-primary);
  text-decoration: none;
  transition: color var(--transition-fast);
}

.breadcrumb a:hover {
  color: var(--color-primary-hover);
}

.breadcrumb span:not(.category-badge) {
  color: var(--color-text-muted);
}

/* Header */
.post-header {
  margin-bottom: var(--spacing-xl);
  padding-bottom: var(--spacing-lg);
  border-bottom: 3px solid var(--color-primary);
}

.category-badge {
  display: inline-block;
  background: var(--color-primary-light);
  color: var(--color-primary);
  padding: var(--spacing-xs) var(--spacing-md);
  border-radius: var(--radius-full);
  font-size: var(--font-size-xs);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.post-header h1 {
  font-size: var(--font-size-3xl);
  color: var(--color-text);
  margin-bottom: var(--spacing-md);
  letter-spacing: -0.02em;
  line-height: 1.3;
}

.post-meta {
  display: flex;
  gap: var(--spacing-lg);
  align-items: center;
  color: var(--color-text-muted);
  font-size: var(--font-size-sm);
  flex-wrap: wrap;
}

/* Content */
.post-content {
  line-height: 1.8;
  color: var(--color-text);
  font-size: var(--font-size-base);
}

.post-content :deep(h3) {
  color: var(--color-text);
  margin: var(--spacing-xl) 0 var(--spacing-md);
  font-size: var(--font-size-xl);
  line-height: 1.4;
}

.post-content :deep(p) {
  margin-bottom: var(--spacing-md);
  color: var(--color-text);
  line-height: 1.8;
}

.post-content :deep(ul) {
  margin-bottom: var(--spacing-md);
  padding-left: var(--spacing-xl);
}

.post-content :deep(li) {
  margin-bottom: var(--spacing-sm);
  color: var(--color-text);
  line-height: 1.6;
}

/* Back link */
.back-link {
  margin-top: var(--spacing-2xl);
  padding-top: var(--spacing-lg);
  border-top: 1px solid var(--color-border);
}

.back-link a {
  color: var(--color-text-muted);
  font-weight: 500;
  font-size: var(--font-size-base);
}

.back-link a:hover {
  color: var(--color-primary);
}

/* Not found */
.not-found {
  text-align: center;
  padding: var(--spacing-2xl) 0;
}

.not-found h1 {
  color: var(--color-error);
  margin-bottom: var(--spacing-md);
  font-size: var(--font-size-2xl);
}

.not-found p {
  color: var(--color-text-muted);
  margin-bottom: var(--spacing-lg);
  font-size: var(--font-size-lg);
}

.not-found a {
  display: inline-block;
  background: var(--color-primary);
  color: white;
  padding: var(--spacing-md) var(--spacing-xl);
  border-radius: var(--radius-md);
  font-weight: 600;
}

.not-found a:hover {
  background: var(--color-primary-hover);
  color: white;
}
</style>

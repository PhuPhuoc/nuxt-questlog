<script setup>
// Mock data
const allPosts = ref([
  {
    id: 1,
    slug: "why-nuxt",
    title: "Tại sao nên dùng Nuxt?",
    excerpt:
      "Nuxt là framework mạnh mẽ built on top of Vue.js với nhiều tính năng ưu việt như SSR, auto-imports, file-based routing.",
    category: "Nuxt",
    date: "15/01/2024",
    views: 100,
  },
  {
    id: 2,
    slug: "vue3-composition-api",
    title: "Hướng dẫn Vue 3 Composition API",
    excerpt:
      "Composition API là cách viết code Vue 3 được khuyến nghị. Học cách dùng ref, computed, watch.",
    category: "Vue",
    date: "10/01/2024",
    views: 250,
  },
  {
    id: 3,
    slug: "typescript-with-nuxt",
    title: "TypeScript với Nuxt cho người mới",
    excerpt:
      "Hướng dẫn setup TypeScript trong Nuxt và những lợi ích khi dùng TypeScript.",
    category: "TypeScript",
    date: "05/01/2024",
    views: 180,
  },
  {
    id: 4,
    slug: "pinia-state-management",
    title: "Pinia - Quản lý State trong Nuxt",
    excerpt: "Tìm hiểu cách dùng Pinia để quản lý state trong ứng dụng Nuxt.",
    category: "Nuxt",
    date: "01/01/2024",
    views: 320,
  },
]);

// SEO
useSeoMeta({
  title: "Blog - BlogTalk",
  description: "Tất cả bài viết về Vue.js, Nuxt, và lập trình web",
});
</script>

<template>
  <div class="blog-page">
    <header class="page-header">
      <h1>Blog</h1>
      <p>Tất cả bài viết về Vue.js, Nuxt, và lập trình web</p>
    </header>

    <div class="posts-grid">
      <article v-for="post in allPosts" :key="post.id" class="post-card">
        <!-- Category badge -->
        <span class="category-badge">{{ post.category }}</span>

        <!-- Title -->
        <NuxtLink :to="`/blog/${post.slug}`">
          <h2>{{ post.title }}</h2>
        </NuxtLink>

        <!-- Excerpt -->
        <p>{{ post.excerpt }}</p>

        <!-- Meta -->
        <div class="post-meta">
          <span>{{ post.date }}</span>
          <span>{{ post.views }} lượt xem</span>
        </div>

        <!-- Read more -->
        <NuxtLink :to="`/blog/${post.slug}`" class="read-more">
          Đọc bài viết
        </NuxtLink>
      </article>
    </div>

    <!-- Link về trang chủ -->
    <div class="back-link">
      <NuxtLink to="/">← Quay về trang chủ</NuxtLink>
    </div>
  </div>
</template>

<style scoped>
.blog-page {
  max-width: 900px;
  margin: 0 auto;
  padding: var(--spacing-2xl) var(--spacing-xl);
}

.page-header {
  text-align: center;
  margin-bottom: var(--spacing-2xl);
}

.page-header h1 {
  font-size: var(--font-size-3xl);
  color: var(--color-text);
  margin-bottom: var(--spacing-sm);
  letter-spacing: -0.02em;
}

.page-header p {
  color: var(--color-text-muted);
  font-size: var(--font-size-lg);
}

/* Grid */
.posts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: var(--spacing-lg);
  margin-bottom: var(--spacing-2xl);
}

.post-card {
  background: var(--color-bg);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--spacing-lg);
  transition: all var(--transition-normal);
}

.post-card:hover {
  border-color: var(--color-primary);
  box-shadow: var(--shadow-md);
}

.category-badge {
  display: inline-block;
  background: var(--color-primary-light);
  color: var(--color-primary);
  padding: var(--spacing-xs) var(--spacing-md);
  border-radius: var(--radius-full);
  font-size: var(--font-size-xs);
  font-weight: 600;
  margin-bottom: var(--spacing-md);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.post-card h2 {
  font-size: var(--font-size-lg);
  margin-bottom: var(--spacing-sm);
  line-height: 1.4;
}

.post-card h2 a {
  color: var(--color-text);
  text-decoration: none;
  transition: color var(--transition-fast);
}

.post-card h2 a:hover {
  color: var(--color-primary);
}

.post-card p {
  color: var(--color-text-muted);
  line-height: 1.6;
  margin-bottom: var(--spacing-md);
  font-size: var(--font-size-sm);
}

.post-meta {
  display: flex;
  gap: var(--spacing-lg);
  font-size: var(--font-size-sm);
  color: var(--color-text-muted);
  margin-bottom: var(--spacing-md);
}

.read-more {
  display: inline-block;
  color: var(--color-primary);
  font-weight: 600;
  font-size: var(--font-size-sm);
}

.read-more:hover {
  color: var(--color-primary-hover);
}

.back-link {
  text-align: center;
}

.back-link a {
  color: var(--color-text-muted);
  font-weight: 500;
}

.back-link a:hover {
  color: var(--color-primary);
}
</style>

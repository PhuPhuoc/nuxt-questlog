<script setup>
// Mock data
const posts = ref([
  {
    id: 1,
    slug: "why-nuxt",
    title: "Tại sao nên dùng Nuxt?",
    excerpt:
      "Nuxt là framework mạnh mẽ built on top of Vue.js với nhiều tính năng ưu việt.",
    date: "15/01/2024",
    views: 100,
  },
  {
    id: 2,
    slug: "vue3-composition-api",
    title: "Hướng dẫn Vue 3 Composition API",
    excerpt:
      "Composition API là cách viết code Vue 3 được khuyến nghị. Học cách dùng ref, computed, watch.",
    date: "10/01/2024",
    views: 250,
  },
  {
    id: 3,
    slug: "typescript-with-nuxt",
    title: "TypeScript với Nuxt cho người mới",
    excerpt:
      "Hướng dẫn setup TypeScript trong Nuxt và những lợi ích khi dùng TypeScript.",
    date: "05/01/2024",
    views: 180,
  },
]);
</script>

<template>
  <div class="home-page">
    <header class="hero">
      <h1>BlogTalk</h1>
      <p>Chào mừng đến với BlogTalk</p>
    </header>

    <section class="latest-posts">
      <h2>Bài viết mới nhất</h2>
      <div class="posts-list">
        <article v-for="post in posts" :key="post.id" class="post-card">
          <h3>{{ post.title }}</h3>
          <p>{{ post.excerpt }}</p>

          <div class="post-meta">
            <span>{{ post.date }}</span>
            <span>{{ post.views }} lượt xem</span>
          </div>

          <NuxtLink :to="`/blog/${post.slug}`" class="read-more">
            Đọc thêm
          </NuxtLink>
        </article>
      </div>
    </section>
    <section class="cta">
      <NuxtLink to="/blog" class="btn-primary"> Xem tất cả bài viết </NuxtLink>
    </section>
  </div>
</template>

<style scoped>
/* Layout */
.home-page {
  max-width: 900px;
  margin: 0 auto;
  padding: var(--spacing-md) var(--spacing-xl);
}

/* Hero */
.hero {
  text-align: center;
  padding: var(--spacing-lg) 0;
  margin-bottom: var(--spacing-2xl);
}

.hero h1 {
  font-size: var(--font-size-4xl);
  color: var(--color-primary);
  margin-bottom: var(--spacing-sm);
  letter-spacing: -0.02em;
}

.hero p {
  font-size: var(--font-size-lg);
  color: var(--color-text-muted);
}

/* Posts */
.latest-posts h2 {
  margin-bottom: var(--spacing-lg);
  font-size: var(--font-size-xl);
  color: var(--color-text);
}

.posts-list {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-lg);
}

.post-card {
  background: var(--color-bg);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--spacing-lg);
  transition:
    box-shadow var(--transition-normal),
    border-color var(--transition-fast);
}

.post-card:hover {
  box-shadow: var(--shadow-md);
  border-color: var(--color-primary);
}

.post-card h3 {
  margin-bottom: var(--spacing-sm);
  color: var(--color-text);
  font-size: var(--font-size-lg);
}

.post-card p {
  color: var(--color-text-muted);
  margin-bottom: var(--spacing-md);
  font-size: var(--font-size-base);
  line-height: 1.6;
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

/* CTA */
.cta {
  text-align: center;
  margin-top: var(--spacing-xl);
}

.btn-primary {
  display: inline-block;
  background: var(--color-primary);
  color: white;
  padding: var(--spacing-md) var(--spacing-xl);
  border-radius: var(--radius-md);
  font-weight: 600;
  transition:
    background var(--transition-fast),
    transform var(--transition-fast);
}

.btn-primary:hover {
  background: var(--color-primary-hover);
  color: white;
  transform: translateY(-1px);
}
</style>

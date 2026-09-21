<script setup>
// SEO: Đặt title và description
useSeoMeta({
  title: "Về Chúng Tôi - BlogTalk",
  description:
    "Tìm hiểu về BlogTalk - blog chia sẻ kiến thức về Vue.js và Nuxt",
});
</script>

<template>
  <div class="about-page">
    <header class="page-header">
      <h1>Về Chúng Tôi</h1>
    </header>

    <article class="about-content">
      <section>
        <h2>BlogTalk là gì?</h2>
        <p>
          BlogTalk là blog cá nhân chia sẻ kiến thức về lập trình web, đặc biệt
          là <strong>Vue.js</strong> và <strong>Nuxt</strong>.
        </p>
        <p>
          Mục tiêu của chúng tôi là tạo ra những bài viết dễ hiểu, thực tế, và
          có thể áp dụng ngay vào project của bạn.
        </p>
      </section>

      <section>
        <h2>Công nghệ sử dụng</h2>
        <ul class="tech-list">
          <li><strong>Nuxt 4</strong> — Framework chính</li>
          <li><strong>Vue 3</strong> — Framework frontend</li>
          <li><strong>TypeScript</strong> — Type safety</li>
          <li><strong>Pinia</strong> — State management</li>
        </ul>
      </section>

      <section>
        <h2>Liên hệ</h2>
        <p>
          Bạn có câu hỏi hoặc muốn đóng góp bài viết? Liên hệ với chúng tôi qua
          email: <a href="mailto:hello@blogtalk.com">hello@blogtalk.com</a>
        </p>
      </section>

      <!-- Link về trang chủ -->
      <div class="back-link">
        <NuxtLink to="/">← Quay về trang chủ</NuxtLink>
      </div>
    </article>
  </div>
</template>

<style scoped>
.about-page {
  max-width: 900px;
  margin: 0 auto;
  padding: var(--spacing-2xl) var(--spacing-xl);
}

.page-header {
  margin-bottom: var(--spacing-xl);
  padding-bottom: var(--spacing-md);
  border-bottom: 3px solid var(--color-primary);
}

.page-header h1 {
  font-size: var(--font-size-3xl);
  color: var(--color-text);
  letter-spacing: -0.02em;
}

.about-content section {
  margin-bottom: var(--spacing-2xl);
}

.about-content h2 {
  font-size: var(--font-size-xl);
  margin-bottom: var(--spacing-md);
  color: var(--color-primary);
}

.about-content p {
  line-height: 1.8;
  margin-bottom: var(--spacing-md);
  color: var(--color-text-muted);
  font-size: var(--font-size-base);
}

.about-content ul {
  list-style: none;
  padding: 0;
}

.about-content li {
  padding: var(--spacing-sm) 0;
  border-bottom: 1px solid var(--color-border);
  color: var(--color-text-muted);
  font-size: var(--font-size-base);
}

.about-content li strong {
  color: var(--color-text);
}

.back-link {
  margin-top: var(--spacing-2xl);
}

.back-link a {
  color: var(--color-text-muted);
  font-weight: 500;
  font-size: var(--font-size-base);
}

.back-link a:hover {
  color: var(--color-primary);
}
</style>

<script setup>
// Navigation links
const navLinks = [
  { label: "Trang chủ", to: "/" },
  { label: "Blog", to: "/blog" },
  { label: "About", to: "/about" },
];
</script>

<template>
  <div class="app-layout">
    <!-- Header với Navigation -->
    <header class="app-header">
      <div class="header-content">
        <NuxtLink to="/" class="logo">BlogTalk</NuxtLink>

        <!-- Navigation Links -->
        <nav class="main-nav">
          <NuxtLink
            v-for="link in navLinks"
            :key="link.to"
            :to="link.to"
            class="nav-link"
          >
            {{ link.label }}
          </NuxtLink>
        </nav>
      </div>
    </header>

    <!-- Main Content -->
    <main class="app-main">
      <slot />
    </main>

    <!-- Footer -->
    <footer class="app-footer">
      <p>© 2024 BlogTalk. Kiến thức chia sẻ - không giới hạn.</p>
    </footer>
  </div>
</template>

<style scoped>
.app-layout {
  min-height: 100vh;
  padding: var(--spacing-md) 0;
  z-index: 100;
}

/* Header */
.app-header {
  background: var(--color-bg);
  border-bottom: 1px solid var(--color-border);
  padding: 1rem 0;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  max-width: 900px;
  margin: 0 auto;
  padding: 0 var(--spacing-xl);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  font-size: var(--font-size-xl);
  font-weight: 700;
  color: var(--color-primary);
  text-decoration: none;
  letter-spacing: -0.02em;
}

.main-nav {
  display: flex;
  gap: var(--spacing-lg);
}

.nav-link {
  color: var(--color-text-muted);
  text-decoration: none;
  padding: var(--spacing-sm) 0;
  font-size: var(--font-size-base);
  font-weight: 500;
  transition: color var(--transition-fast);
}

.nav-link:hover {
  color: var(--color-primary);
}

/* Active state - tự động thêm bởi NuxtLink */
.nav-link.router-link-active {
  color: var(--color-primary);
  font-weight: 600;
}

/* Main */
.app-main {
  flex: 1;
}

/* Footer */
.app-footer {
  background: var(--color-surface);
  padding: var(--spacing-xl);
  text-align: center;
  color: var(--color-text-muted);
  margin-top: var(--spacing-md);
  border-top: 1px solid var(--color-border);
}

.app-footer p {
  font-size: var(--font-size-sm);
}
</style>

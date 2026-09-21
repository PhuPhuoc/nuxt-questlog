<script setup>
// root component
</script>

<template>
  <!-- 1. Accessibility -->
  <NuxtRouteAnnouncer />
  <!-- Screen readers sẽ thông báo khi route thay đổi -->

  <!-- 2. Layout wrapper -->
  <NuxtLayout>
    <!-- Bọc content với layout (mặc định: layouts/default.vue) -->

    <!-- 3. Page content -->
    <NuxtPage />
    <!-- Hiển thị nội dung của PAGE HIỆN TẠI -->
    <!-- Khi URL = / → render pages/index.vue -->
    <!-- Khi URL = /about → render pages/about.vue -->
  </NuxtLayout>
</template>

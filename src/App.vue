<script setup>
import SiteNav from "@/components/SiteNav.vue";
import SiteFooter from "./components/SiteFooter.vue";
</script>

<template>
  <div class="flex flex-col h-screen">
    <SiteNav />
    <main class="flex-grow">
      <RouterView />
    </main>
    <SiteFooter />
  </div>
</template>

<style></style>

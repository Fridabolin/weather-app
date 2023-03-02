<script setup>
import { RouterView } from "vue-router";
import SiteNavigationVue from "./components/SiteNavigation.vue";
</script>

<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigationVue></SiteNavigationVue>
    <RouterView />
  </div>
</template>

<style scoped></style>

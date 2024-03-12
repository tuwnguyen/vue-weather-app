<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-gradient-to-r from-sky-500 to-indigo-500">
    <SiteNavigation />
    <RouterView v-slot="{ Component }">
      <Transition name="page" mode="out-in">
        <component :is="Component"></component>
      </Transition>
    </RouterView>
  </div>
</template>
<script setup>
  import { RouterView } from 'vue-router';
  import SiteNavigation from './components/SiteNavigation.vue'
</script>
<style>
.page-enter-active,
.page-leave-active {
  transition: 600ms ease all;
}

.page-enter-from,
.page-leave-to {
  opacity: 0;
}
</style>
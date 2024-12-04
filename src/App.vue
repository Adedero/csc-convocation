<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header class="bg-green-500 py-2 px-4 sticky top-0 z-10">
    <router-link to="/" class="flex items-center gap-2 text-white">
      <img class="w-5" src="./assets/logo.png">
      <p class="text-3xl">Tech<span class="font-bold">Titans</span></p>
    </router-link>
  </header>

  <main class="flex justify-center p-3">
    <RouterView />
  </main>

  <hr class="my-6" />

  <footer class="p-3 text-sm text-center">
    <p>
      Powered by <span class="font-semibold">Adedero Cosmos</span>
    </p>
    <p>&copy; Tech<span class="font-semibold">Titans</span> 2024. All Rights Reserved</p>
  </footer>
</template>


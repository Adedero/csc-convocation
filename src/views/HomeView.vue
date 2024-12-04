<script setup>
import { computed, ref } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

const user = ref({
  name: "",
  cgpa: null
});

const disabled = computed(() => {
  return (
    !user.value.name ||
    user.value.cgpa < 0 ||
    user.value.cgpa > 5
  );
});

const handleClick = () => {
  sessionStorage.setItem("graduate", JSON.stringify(user.value));
  router.push({ name: "picture" });
}
</script>

<template>
  <section>
    <h1 class="text-3xl font-bold">Convocation Image Generator</h1>

    <div class="mt-4 p-5 grid gap-6">
      <div>
        <label for="name">Name</label>
        <input v-model="user.name" id="name" type="text" placeholder="Enter your name" class="w-full">
      </div>

      <div>
        <label for="cgpa">CGPA</label>
        <div class="flex items-center gap-1">
          <input v-model="user.cgpa" id="cgpa" type="number" :min="0" :max="5" class="w-full">/
          <input type="number" value="5.0" disabled class="w-full">
        </div>
      </div>

      <button @click="handleClick" type="button" :disabled
        class="bg-green-500 hover:bg-green-600 transition-colors
        text-white py-2 px-4 rounded disabled:bg-slate-400">
        Done
      </button>

    </div>
  </section>
</template>

<style scoped>
input {
  @apply border border-gray-300 py-2 px-4 rounded text-slate-600 text-lg transition-all;
  @apply hover:border-green-500 hover:shadow-sm;
}
</style>

<script setup>
import { computed, onMounted, ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';
import futo_logo from '@/assets/futo-logo.png';
import titans_logo from '@/assets/logo.png';
//import html2canvas from "html2canvas";
import * as htmlToImage from 'html-to-image';

const router = useRouter();
const user = ref(null);

const image = ref();
const el = ref();

const options = ref({
  showFutoLogo: true,
  showTitansLogo: true,
  showCGPA: true,
  showUniName: false,
  showDegree: true
});


const degree = computed(() => {
  if (!user.value || !user.value.cgpa) return;

  const cgpa = user.value.cgpa;

  if (cgpa >= 4.5 && cgpa <= 5.0) {
    return 'First Class Honours';
  } else if (cgpa >= 3.5 && cgpa < 4.5) {
    return 'Second Class Honours (Upper Division)';
  } else if (cgpa >= 2.4 && cgpa < 3.5) {
    return 'Second Class Honours (Lower Division)';
  } else if (cgpa >= 1.5 && cgpa < 2.4) {
    return 'Third Class Honours';
  } else if (cgpa >= 1.0 && cgpa < 1.5) {
    return 'Pass';
  } else if (cgpa >= 0.0 && cgpa < 1.0) {
    return 'Fail';
  } else {
    return 'Invalid CGPA';
  }
});

const loading = ref(false);
const err = ref(false);
const file = ref(null);
const fileUrl = ref(null);

const onInput = (event) => {
  err.value = false;
  loading.value = true;
  fileUrl.value = null;
  const files = event.target.files;
  file.value = files[0];

  const isImage = file.value.type.includes('image');

  if (!isImage) {
    err.value = true;
    loading.value = false;
    return;
  }

  const reader = new FileReader();
  reader.onload = () => {
    fileUrl.value = reader.result;
    if (image.value && image.value.complete) {
      loading.value = false;
    }
  };
  reader.readAsDataURL(file.value);
}

const downloading = ref(false);

const download = () => {
  downloading.value = true;
  if (!el.value) return;

  downloading.value = true;
  const timestamp = Date.now().toString();
  htmlToImage.toPng(el.value)
  .then(function (dataUrl) {
    var link = document.createElement('a');
    link.download = `${user.value.name}-${timestamp}.png`;
    link.href = dataUrl;
    link.click();
    downloading.value = false;
  });
  /* html2canvas(el.value, { scale: 0.9 }).then(canvas => {
    const link = document.createElement('a');
    link.href = canvas.toDataURL('image/png');
    link.download = `${user.value.name}-${timestamp}.png`;
    link.click();
    downloading.value = false;
  }); */
}

onMounted(() => {
  const item = sessionStorage.getItem("graduate");
  if (!item) {
    router.push("/");
    return;
  }
  try {
    const graduate = JSON.parse(item);
    user.value = graduate;
  } catch (error) {
    console.log(error);
    router.push("/");
    return;
  }
});
</script>

<template>
  <section v-if="user" class="w-full max-w-[30rem] grid">
    <router-link to="/" class="bg-blue-500 text-white hover:bg-blue-600 py-2 px-3
      rounded-md mb-1 ml-auto">
      Back
    </router-link>

    <div class="grid gap-1">
      <label for="picture">Upload your photo</label>
      <input @input="onInput" id="picture" type="file" accept="image/*"  class="w-full"/>
      <small v-if="err" class="text-sm text-red-500">
        File must be an image.
      </small>
    </div>
    <hr class="my-4" />

    <div v-if="loading" class="h-60 rounded-md flex items-center justify-center bg-white/50 backdrop-blur">
      <div class="loader"></div>
    </div>

    <div v-show="fileUrl" class="flex justify-end mb-4 flex-wrap gap-2">
      <button @click="download" type="button" :disabled="downloading"
        class="bg-green-500 hover:bg-green-600 transition-colors
        text-white py-2 px-4 rounded disabled:bg-slate-400">
        {{ downloading ? 'Downloading...' : 'Download' }}
      </button>

      <div
        class="hidden w-full rounded-md border border-blue-500 bg-blue-50 text-blue-500 p-1.5
         justify-between">
        <div>
          Download started. Please wait...
        </div>

        <span
          class="font-semibold border border-blue-500 w-6 h-6 flex items-center justify-center
          rounded-full text-center cursor-pointer select-none hover:bg-blue-100">
          x
        </span>
      </div>
    </div>

    <div id="el" ref="el" v-show="fileUrl" class="relative">
      <div class="absolute top-5 left-5 w-[3rem]">
        <img v-if="options.showFutoLogo" :src="futo_logo" alt="FUTO" class="w-full">
      </div>

      <div class="absolute top-5 right-5 w-8 h-12">
        <img v-if="options.showTitansLogo" :src="titans_logo" alt="Titans" class="w-full h-full">
      </div>

      <img ref="image" :src="fileUrl" :alt="user.name" class="w-full">

      <div class="absolute bottom-5 left-1/2 -translate-x-1/2 grid gap-1 w-[90%]">
        <div class="h-[3px] bg-[#2ccb31] w-[40%] rounded-md"></div>

        <div
          class="font-['Times_New_Roman'] font-semibold uppercase text-[0.8rem]
          bg-white/70 backdrop-blur text-center py-1 px-2
          rounded-md w-full" :class="{ 'text-[0.75rem]' : options.showUniName }">
          <p class="text-lg font-bold" :class="{ 'text-[1rem]' : options.showUniName }">
            {{ user.name }}
          </p>
          <p>Department of computer science</p>
          <p v-if="options.showUniName">Federal university of technology, Owerri</p>
          <p>
            <span v-if="options.showDegree">{{ degree }}</span>
            <span v-if="options.showCGPA"> ({{ user.cgpa }}/5.0)</span>
          </p>
          <p class="italic">bachelor of technology
            (B.<span class="lowercase"><span class="uppercase">T</span>ech.</span>)
          </p>
          <p class="italic">Class of 2022/2023</p>
        </div>

        <div class="h-[3px] bg-[#ffe331] w-[40%] rounded-md justify-self-end"></div>
      </div>
    </div>

    <hr class="my-4" />

    <div>
      <h1 class="font-semibold text-lg">Options</h1>
      <div class="grid gap-1">
        <div class="flex items-center gap-2">
          <input v-model="options.showFutoLogo" type="checkbox" id="show-futo-logo">
          <label for="show-futo-logo">Show FUTO logo</label>
        </div>

        <div class="flex items-center gap-2">
          <input v-model="options.showTitansLogo" type="checkbox" id="show-titans-logo">
          <label for="show-titans-logo">Show Titans logo</label>
        </div>

        <div class="flex items-center gap-2">
          <input v-model="options.showDegree" type="checkbox" id="show-degree">
          <label for="show-degree">Show Degree</label>
        </div>

        <div class="flex items-center gap-2">
          <input v-model="options.showCGPA" type="checkbox" id="show-cgpa">
          <label for="show-cgpa">Show CGPA</label>
        </div>

        <div class="flex items-center gap-2">
          <input v-model="options.showUniName" type="checkbox" id="show-uni-name">
          <label for="show-uni-name">Show university name</label>
        </div>
      </div>
    </div>

  </section>
</template>

<style scoped>
input {
  @apply border border-gray-300 py-2 px-4 rounded  text-slate-600 text-lg transition-all;
  @apply hover:border-green-500 hover:shadow-sm;
}

/* HTML: <div class="loader"></div> */
.loader {
  width: 50px;
  padding: 8px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: #25b09b;
  --_m:
    conic-gradient(#0000 10%,#000),
    linear-gradient(#000 0 0) content-box;
  -webkit-mask: var(--_m);
          mask: var(--_m);
  -webkit-mask-composite: source-out;
          mask-composite: subtract;
  animation: l3 1s infinite linear;
}
@keyframes l3 {to{transform: rotate(1turn)}}
</style>

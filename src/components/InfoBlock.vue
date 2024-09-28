<script setup>
import { inject } from 'vue'

defineProps({
  title: String,
  description: String,
  imageUrl: String
})
const { closeDrawer } = inject('cart')
</script>

<template>
  <div class="flex flex-col items-center text-center w-72 mx-auto">
    <img height="80" width="80" :src="imageUrl" :alt="title" />
    <h2 class="mt-4 text-xl font-medium">{{ title }}</h2>
    <p class="text-gray-400 mt-2">{{ description }}</p>
    <button
      @click="closeDrawer"
      class="mt-4 bg-lime-500 w-full rounded-xl cursor-pointer py-3 text-white hover:bg-lime-600 transition active:bg-lime-700 disabled:bg-slate-300"
    >
      Back to sneakers
    </button>
  </div>
</template>

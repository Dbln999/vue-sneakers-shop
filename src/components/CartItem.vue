<script setup>
defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number
})

const emit = defineEmits(['removeFromCart'])
</script>

<template>
  <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
    <img :src="imageUrl" class="w-16 h-16" :alt="title" />
    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>
      <div class="flex justify-between mt-2">
        <b class="flex-1">${{ price.toFixed(2) }}</b>
        <img
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
          src="/close.svg"
          alt="close button"
          @click="emit('removeFromCart')"
        />
      </div>
    </div>
  </div>
</template>

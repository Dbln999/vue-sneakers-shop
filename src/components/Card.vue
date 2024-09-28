<script setup lang="ts">
defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function
})
</script>

<template>
  <div
    class="relative border bg-white border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
  >
    <img
      v-if="onClickFavorite"
      :src="isFavorite ? '/like-2.svg' : '/like-1.svg'"
      alt="Like"
      class="absolute top-5 left-5"
      @click="onClickFavorite"
    />

    <img :src="imageUrl" alt="Sneaker" />

    <p class="mt-2">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Price: </span>
        <b>${{ price.toFixed(2) }}</b>
      </div>
      <img
        v-if="onClickAdd"
        :src="!isAdded ? '/plus.svg' : '/checked.svg'"
        alt="Plus"
        @click="onClickAdd"
      />
    </div>
  </div>
</template>

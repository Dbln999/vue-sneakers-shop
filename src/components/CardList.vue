<script setup>
import Card from '@/components/Card.vue'
const props = defineProps({
  items: Array,
  isFavorites: Boolean
})

console.log(props.items)
const emit = defineEmits(['addToFavorite', 'addToCart'])
// const addToFavorite = inject('addToFavorite')
</script>
<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :id="item.id"
      :key="item.id"
      :title="item.title"
      :image-url="item.imageUrl"
      :price="item.price"
      :on-click-favorite="isFavorites ? null : () => emit('addToFavorite', item)"
      :on-click-add="isFavorites ? null : () => emit('addToCart', item)"
      :is-favorite="item.isFavorite"
      :is-added="item.isAdded"
    />
  </div>
</template>

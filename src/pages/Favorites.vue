<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'

const favorites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get(`${import.meta.env.VITE_API}/favorites?_relations=items`)
    favorites.value = data.map((obj) => obj.item)
    console.log(data)
  } catch (e) {
    console.error(e)
  }
})
</script>
<template>
  <h2 class="text-3xl font-bold mb-8">My favorites</h2>
  <CardList :items="favorites" :is-favorites="true" />
</template>

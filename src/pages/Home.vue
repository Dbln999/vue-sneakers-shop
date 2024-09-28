<script setup>
import CardList from '@/components/CardList.vue'
import axios from 'axios'
import debounce from 'lodash.debounce'
import { inject, onMounted, reactive, ref, watch } from 'vue'

const { cart, addToCart, removeFromCart } = inject('cart')
const items = ref([])
const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = (e) => {
  filters.sortBy = e.target.value
}

const onChangeSearchInput = debounce((e) => {
  filters.searchQuery = e.target.value
}, 300)

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get(`${import.meta.env.VITE_API}/items`, {
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      isAdded: false,
      favoriteId: null
    }))
  } catch (e) {
    console.error(e)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(`${import.meta.env.VITE_API}/favorites`)
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => item.id === favorite.item_id)

      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (e) {
    console.error(e)
  }
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id
      }
      const { data } = await axios.post(`${import.meta.env.VITE_API}/favorites`, obj)
      item.isFavorite = true
      item.favoriteId = data.id
    } else {
      await axios.delete(`${import.meta.env.VITE_API}/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (e) {
    console.error(e)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)
</script>
<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">All sneakers</h2>

    <div class="flex gap-4">
      <select
        @change="onChangeSelect"
        class="py-2 px-3 border rounded-md outline-none focus:border-gray-400"
      >
        <option value="name">By name</option>
        <option value="price">By price (cheapest)</option>
        <option value="-price">By price (expensive)</option>
      </select>

      <div class="relative">
        <img class="absolute left-3 top-3" src="/search.svg" alt="Search icon" />
        <input
          @change="onChangeSearchInput"
          type="text"
          placeholder="Search..."
          class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400"
        />
      </div>
    </div>
  </div>
  <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
</template>

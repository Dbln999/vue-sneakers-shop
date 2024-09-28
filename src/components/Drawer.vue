<script setup lang="ts">
import DrawerHead from '@/components/DrawerHead.vue'
import CartItemList from '@/components/CartItemList.vue'
import { computed, inject, ref } from 'vue'
import InfoBlock from '@/components/InfoBlock.vue'
import axios from 'axios'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number
})

const isCreating = ref(false)
const orderId = ref(null)
const orderButtonDisabled = computed(() => isCreating.value || cart.value.length === 0)

const { cart, closeDrawer } = inject('cart')

const createOrder = async () => {
  try {
    isCreating.value = true
    const { data } = await axios.post(`${import.meta.env.VITE_API}/orders`, {
      items: cart.value.map((item) => item.id),
      totalPrice: props.totalPrice.value
    })

    cart.value = []
    orderId.value = data.id

    return data
  } catch (e) {
    console.error(e)
  } finally {
    isCreating.value = false
  }
}
</script>

<template>
  <div class="fixed top-0 left-0 w-full h-full bg-black/50 z-10" @click="closeDrawer"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />
    <CartItemList />

    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock
        v-if="!totalPrice && !orderId"
        title="Cart is empty"
        description="Add at least one pair of sneakers to place an order."
        image-url="/package-icon.png"
      />
      <InfoBlock
        v-if="orderId"
        title="Order is placed!"
        :description="`Your order #${Date.now()} has been placed and soon will be delivered to your address.`"
        image-url="/order-success-icon.png"
      />
    </div>

    <div v-if="totalPrice" class="flex flex-col gap-5 mt-7">
      <div class="flex gap-2">
        <span>Overall:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>${{ totalPrice.toFixed(2) }}</b>
      </div>
      <div class="flex gap-2">
        <span>Fee 5%:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>${{ vatPrice }}</b>
      </div>
      <button
        :disabled="orderButtonDisabled"
        @click="createOrder"
        class="mt-4 bg-lime-500 w-full rounded-xl cursor-pointer py-3 text-white hover:bg-lime-600 transition active:bg-lime-700 disabled:bg-slate-300"
      >
        Place an order
      </button>
    </div>
  </div>
</template>

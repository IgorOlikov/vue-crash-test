<script setup>
import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import {computed} from "vue";
import InfoBlock from "@/components/InfoBlock.vue";

defineProps({
  totalPrice: Number,
  vatPrice: Number,
  //isCreatedOrder: Boolean,
  cartButtonDisabled: Boolean
});

const emit = defineEmits(['createOrder']);



</script>

<template>

  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70">
  </div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
  <DrawerHead/>

  <InfoBlock
      title="Корзина пустая"
      description="Пополните корзину"
      image-url="/package-icon.png"
  />

  <CartItemList/>

    <div class="flex flex-col gap-4 mt-7">

      <div class="flex gap-2">
        <span>Итого:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ totalPrice }} руб.</b>
      </div>

        <div class="flex gap-2">
          <span>Налог: 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} руб.</b>
      </div>

      <button
          :disabled="cartButtonDisabled"
          @click="() => emit('createOrder')"
          class="transition mt-4 bg-lime-500 w-full cursor-pointer disabled:bg-slate-400 rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700">
        Оформить заказ
      </button>

    </div>
  </div>



</template>

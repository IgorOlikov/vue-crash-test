<script setup>
import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import {computed, ref, inject} from "vue";
import InfoBlock from "@/components/InfoBlock.vue";
import axios from "axios";

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
});

const { cart, closeDrawer, } = inject('cart');

const orderId = ref(null);

const isCreatedOrder = ref(false);

const cartIsEmpty = computed(() => cart.value.length === 0);

const buttonDisabled = computed(() =>
    isCreatedOrder.value || cartIsEmpty.value
);

const createOrder = async () => {
  try {
    isCreatedOrder.value = true;

    const { data } = await axios.post(`https://221dbf5fb9a84a5d.mokky.dev/orders`,{
      items: cart.value,
      totalPrice: props.totalPrice.value,
    })
    cart.value = [];

    orderId.value = data.id;
    console.log(orderId.value)

  }catch (err){
    console.log(err);
  } finally {
    isCreatedOrder.value = false;
  }
};

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70">
  </div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
  <DrawerHead/>

  <div v-if="!totalPrice || orderId" class="flex h-full items-center">
    <InfoBlock
        v-if="!totalPrice && !orderId"
        title="Корзина пустая"
        description="Пополните корзину"
        image-url="/package-icon.png"
    />
    <InfoBlock
      v-if="orderId"
      title="Заказ оформлен!!!"
      :description="`Ваш заказ  под номером Id-${orderId} скоро будет передан курьерской доставке`"
      image-url="/order-success-icon.png"
    />
  </div>

  <div v-else>
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
          :disabled="buttonDisabled"
          @click="createOrder"
          class="transition mt-4 bg-lime-500 w-full cursor-pointer disabled:bg-slate-400 rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700">
        Оформить заказ
      </button>
    </div>
    </div>
  </div>



</template>

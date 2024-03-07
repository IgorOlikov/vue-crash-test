<script setup>
  import {computed, provide, ref, watch} from "vue";
  import axios from "axios";
  import Header from "@/components/Header.vue";
  import Drawer from "@/components/Drawer.vue";
  import Home from "@/pages/Home.vue";


  const cart = ref([]);
  const drawerOpen = ref(false);
  const isCreatedOrder = ref(false);
  

  const createOrder = async () => {
    try {
      isCreatedOrder.value = true;
      const { data } = await axios.post(`https://221dbf5fb9a84a5d.mokky.dev/orders`,{
        items: cart.value,
        totalPrice: totalPrice.value,
      })
      cart.value = [];

      return data;
    }catch (err){
      console.log(err);
    } finally {
      isCreatedOrder.value = false;
    }
  };

  const cartIsEmpty = computed(() => cart.value.length === 0);

  const cartButtonDisabled = computed(() =>
    isCreatedOrder.value || cartIsEmpty.value
  );

  const totalPrice = computed(
      () => cart.value.reduce((acc, item) => acc + item.price, 0)
    );

  const vatPrice = computed(() => Math.round(totalPrice.value * 0.05));


  const closeDrawer = () => {
    drawerOpen.value = false;
  }

  const openDrawer = () => {
    drawerOpen.value = true;
  }

  const addToCart = (item) => {
    cart.value.push(item);
    item.isAdded = true;
  };

  const removeFromCart = (item) => {
    cart.value.splice(cart.value.indexOf(item),1);
    item.isAdded = false;
  }

  const addToFavorite = async (item) => {
    try {
      if (!item.isFavorite) {
        const obj = {
          parentId: item.id,
        };

        item.isFavorite = true;

        const {data} = await axios.post(`https://221dbf5fb9a84a5d.mokky.dev/favorites`, obj)

        item.isFavorite = true;
        item.favoriteId = data.id;

        console.log(data);
      } else {
        item.isFavorite = false;

        await axios.delete(`https://221dbf5fb9a84a5d.mokky.dev/favorites/${item.favoriteId}`)

        item.favoriteId = null;
      }
    }catch (err){

    }
  }

  watch(cart, () => {
    localStorage.setItem('cart', JSON.stringify(cart.value));
  },
      { deep: true }
  );

  provide('cart',{
    cart,
    closeDrawer,
    openDrawer,
    addToCart,
    removeFromCart,
  });
</script>

<template>

  <Drawer
      v-if="drawerOpen"
      :total-price="totalPrice"
      :vat-price="vatPrice"
      :cart-button-disabled="cartButtonDisabled"
      @create-order="createOrder"
  />

  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14 ">
    <Header
        :total-price="totalPrice"
        @open-drawer="openDrawer"
    />

    <div class="p-10">
      <Home/>

    </div>

  </div>

</template>


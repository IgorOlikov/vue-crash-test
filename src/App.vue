<script setup>
  import {computed, onMounted, provide, reactive, ref, watch} from "vue";
  import axios from "axios";

  import Header from "@/components/Header.vue";
  import CardList from "@/components/CardList.vue";
  import Drawer from "@/components/Drawer.vue";

  const items = ref([]);

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

  const filters = reactive({
    sortBy: 'title',
    searchQuery: ''
  });

  const closeDrawer = () => {
    drawerOpen.value = false;
  }

  const openDrawer = () => {
    drawerOpen.value = true;
  }

  const onClickAddPlus = (item) => {
    if (!item.isAdded){
      addToCart(item);
      }else {
      removeFromCart(item);
    }
  };

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

  const fetchItems = async () => {
    try{
      const params = {
        sortBy: filters.sortBy,
      };
      if (filters.searchQuery){
        params.title = `*${filters.searchQuery}*`
      }

      const { data } = await axios.get(
          `https://221dbf5fb9a84a5d.mokky.dev/items`,
          {
            params
          })
      items.value = data.map((obj) => ({
        ...obj,
        isFavorite:false,
        isAdded:false,
        favoriteId: null
      }));
    } catch (err) {
      console.log(err)
    }
  };

  const fetchFavorites = async () => {
    try{
      const { data: favorites } = await axios.get(
        `https://221dbf5fb9a84a5d.mokky.dev/favorites`
      )
      items.value = items.value.map(item => {
          const favorite = favorites.find((favorite) => favorite.parentId === item.id);

          if (!favorite) {
            return item;
          }

          return {
            ...item,
            isFavorite: true,
            favoriteId: favorite.id,
          }
      });
      //console.log(items.value);
    }   catch (err) {
      console.log(err)
    }
}

  const onChangeSelect = event => {
    filters.sortBy = event.target.value;
  }

  const onChangeSearchInput = event => {
    filters.searchQuery = event.target.value;
  }

  onMounted(async () => {
    await fetchItems();
    await fetchFavorites();
  });
  watch(filters, fetchItems);

  watch(cart, () => {
    items.value = items.value.map((item) => ({
      ...item,
      isAdded: false
    }))
  });

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
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb10">Все кроссовки</h2>

        <div class="flex gap-4">
          <select
              @change="onChangeSelect"
              class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>

          <div class="relative">
            <img
                class="absolute left-3 top-3"
                src="/search.svg"
            />
            <input
                @input="onChangeSearchInput"
                class="border rounded-md py-2 pl-11 pr-4 focus:border-gray-400 border-gray-200 outline-none"
                placeholder="Поиск"/>
          </div>
        </div>

      </div>
      <div class="mt-10">
        <CardList
            :items="items"
            @add-to-favorite="addToFavorite"
            @add-to-cart="onClickAddPlus"
        />
      </div>

    </div>

  </div>

</template>


<script setup>
  import {onMounted, provide, reactive, ref, watch} from "vue";
  import axios from "axios";

  import Header from "@/components/Header.vue";
  import CardList from "@/components/CardList.vue";
  import Drawer from "@/components/Drawer.vue";

  const items = ref([]);

  const drawerOpen =ref(false);


  const filters = reactive({
    sortBy: 'title',
    searchQuery: ''
  });


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

</script>

<template>


  <Drawer v-if="drawerOpen" />


  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14 ">
    <Header/>

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
        <CardList :items="items" @addToFavorite="addToFavorite"/>
      </div>


    </div>

  </div>

</template>


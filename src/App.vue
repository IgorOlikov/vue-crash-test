<script setup>
import {onMounted, reactive, ref, watch} from "vue";
  import axios from "axios";

  import Header from "@/components/Header.vue";
  import CardList from "@/components/CardList.vue";
  import Drawer from "@/components/Drawer.vue";

  const items = ref([]);

  const filters = reactive({
    sortBy: '',
    searchQuery: ''
  });

  const fetchItems = async () => {
    try{

      const params = {
        sortBy: filters.sortBy,
        searchQuery: filters.searchQuery
      };

      const { data } = await axios.
      get('https://221dbf5fb9a84a5d.mokky.dev/items?sortBy=' + filters.sortBy)
      items.value = data;
    } catch (err) {
      console.log(err)
    }
  };

  const onChangeSelect = event => {
    filters.sortBy = event.target.value;
  }

  onMounted(fetchItems);
  watch(filters, fetchItems);
</script>

<template>


  <!-- <Drawer/> -->


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
                class="border rounded-md py-2 pl-11 pr-4 focus:border-gray-400 border-gray-200 outline-none"
                placeholder="Поиск"/>
          </div>
        </div>

      </div>
      <div class="mt-10">
        <CardList :items="items"/>
      </div>


    </div>

  </div>

</template>


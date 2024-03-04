<script setup>
  import Header from "@/components/Header.vue";
  import CardList from "@/components/CardList.vue";
  import Drawer from "@/components/Drawer.vue";
</script>

<template>


  <!-- <Drawer/> -->


  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14 ">
    <Header/>

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb10">Все кроссовки</h2>

        <div class="flex gap-4">
          <select class="py-2 px-3 border rounded-md outline-none">
            <option>По названию</option>
            <option>По цене (дешевые)</option>
            <option>По цене (дорогие)</option>
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

      <CardList/>

    </div>

  </div>

</template>


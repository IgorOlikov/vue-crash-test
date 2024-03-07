<script setup>
import axios from "axios";
import {onMounted, ref} from "vue";
import CardList from "@/components/CardList.vue";

const favorites = ref([]);

onMounted(async () => {
  try {
    const { data } = await axios.get(
        `https://221dbf5fb9a84a5d.mokky.dev/favorites`
    );

    favorites.value = data.map(obj => obj.item);
  } catch (err){

  }
});


</script>

<template>
  <h2 class="text-3xl font-bold mb-10">Избранное</h2>

  <CardList
      is-favorites
    :items="favorites"

  />
  <!-- @add-to-favorite="addToFavorite"
    @add-to-cart="onClickAddPlus"  -->

</template>
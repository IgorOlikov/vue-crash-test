<script setup>
import Card from "@/components/Card.vue";

defineProps({
  items: Array,
});


const onClickAdd = () => alert('Добавить');
const onClickFavorite = () => alert('Избранное');
</script>

<template>

  <div class="grid grid-cols-4 gap-5">
    <Card
        v-for="item in items"
        :key="item.id"
        :title="item.title"
        :price="item.price"
        :image-url="item.imageUrl"

    />
  </div>

</template>

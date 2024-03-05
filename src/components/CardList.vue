<script setup>
import Card from "@/components/Card.vue";


defineProps({
  items: Array,
});

const emit = defineEmits(['addToFavorite']);


</script>

<template>

  <div class="grid grid-cols-4 gap-5">
    <Card
        v-for="item in items"
        :id="item.id"
        :key="item.id"
        :title="item.title"
        :price="item.price"
        :image-url="item.imageUrl"
        :onClickFavorite="() => emit('addToFavorite', item)"
        :is-favorite="item.isFavorite"


    />
  </div>

</template>

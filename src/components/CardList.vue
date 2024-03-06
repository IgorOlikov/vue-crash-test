<script setup>
import Card from "@/components/Card.vue";


defineProps({
  items: Array,
});

const emit = defineEmits(['addToFavorite','addToCart']);


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
        :on-click-favorite="() => emit('addToFavorite', item)"
        :on-click-add="() => emit('addToCart', item)"
        :is-favorite="item.isFavorite"
        :is-added="item.isAdded"


    />
  </div>

</template>

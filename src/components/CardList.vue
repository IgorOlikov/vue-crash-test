<script setup>
import Card from "@/components/Card.vue";


defineProps({
  items: Array,
  isFavorites: Boolean,
});

const emit = defineEmits(['addToFavorite','addToCart']);



</script>

<template>

  <div
      v-auto-animate
      class="grid grid-cols-4 gap-5">
    <Card
        v-for="item in items"
        :id="item.id"
        :key="item.id"
        :title="item.title"
        :price="item.price"
        :image-url="item.imageUrl"
        :on-click-favorite="isFavorites ? null : () => emit('addToFavorite', item)"
        :on-click-add="isFavorites ? null : () => emit('addToCart', item)"
        :is-favorite="item.isFavorite"
        :is-added="item.isAdded"


    />
  </div>

</template>

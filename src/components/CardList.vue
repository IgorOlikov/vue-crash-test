<script setup>
import Card from "@/components/Card.vue";

const onClickAdd = () => alert('Добавить');
const onClickFavorite = () => alert('Избранное');
</script>

<template>

  <div class="grid grid-cols-4 gap-5">
    <Card
        title="Nike air max"
        :price="5000"
        image-url="/sneakers/sneakers-1.jpg"
        :is-added="false"
        :is-favorite="false"
        :on-click-add="onClickAdd"
        :on-click-favorite="onClickFavorite"
    />
  </div>

</template>

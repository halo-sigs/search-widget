<script setup lang="ts">
import { SearchWidget } from "@halo-dev/search-widget";
import "@halo-dev/search-widget/dist/style.css";
import { onMounted, ref } from "vue";

const visible = ref(false);
onMounted(() => {
  visible.value = true;
});
</script>

<template>
  <SearchWidget v-model:visible="visible" />
</template>

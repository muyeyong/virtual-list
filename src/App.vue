<script setup lang="ts">
import { ref } from "vue";
import FixedListItem from "./components/FixedListItem.vue";
import VarListItem from "./components/VarListItem.vue";

let currComponent = ref(FixedListItem);

const handleComponentChange = (key: string) => {
  switch (key) {
    case "fixed":
      currComponent.value = FixedListItem;
      break;
    case "var":
      currComponent.value = VarListItem;
      break;
    default:
      break;
  }
};
</script>

<template>
  <main class="main">
    <button @click="handleComponentChange('fixed')">固定长度项列表</button>
    <button @click="handleComponentChange('var')">可变长度列表</button>
    <component :is="currComponent" />
  </main>
</template>

<style scoped>
.main {
  margin: 0 auto;
}
</style>

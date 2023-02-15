<script setup lang="ts">
import { ref, onMounted } from "vue";

const list = ref([1, 2, 3]);

const itemRefs = ref([]);

onMounted(() => {
  console.log(itemRefs.value.map((i: any) => i.textContent));
});
</script>

<template>
  <ul>
    <li v-for="item in list" :ref="(el) => itemRefs.push(el)" :key="item">
      {{ item }}
    </li>
  </ul>
</template>

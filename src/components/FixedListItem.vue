<script setup lang="ts">
import { reactive, ref, onMounted, nextTick } from "vue";

interface IState {
  startOffset: number;
  endOffset: number;
  visibleData: any[];
}

interface ICache {
  top: number;
  bottom: number;
  index: number;
}
const count = 10000;
const itemHeight = 60;
const bufferSize = 5;

const state: IState = reactive({
  startOffset: 0,
  endOffset: 0,
  visibleData: [],
});

let cache: ICache[] = [];
let anchorIndex: ICache = { top: 0, bottom: 0, index: 0 };
let startIndex = ref(0);
let endIndex = ref(0);
let preScrollTop = 0;
const data = new Array(count).fill(true);
let visibleCount = 0;
const mainRef = ref();
const itemRefs: any[] = [];

onMounted(() => {
  visibleCount =
    Math.ceil(mainRef.value.offsetHeight / itemHeight) + bufferSize;
  endIndex.value = startIndex.value + visibleCount;
  updateVisibleData();
  nextTick(() => {
    updateCache();
  });
});

const updateCache = () => {
  const array: ICache[] = [];
  itemRefs.forEach((el, index) => {
    const rect = el.getBoundingClientRect();
    const top = mainRef.value.scrollTop + rect.top;
    array.push({ top, bottom: top + itemHeight, index });
  });
  return array;
};

const updateVisibleData = () => {
  const endOffset = (data.length - endIndex.value) * itemHeight;
  const visibleData = data.slice(startIndex.value, endIndex.value);
  state.startOffset = anchorIndex.top;
  state.endOffset = endOffset;
  state.visibleData = visibleData;
};

const updateIndex = (scroll: number) => {
  // const cache =
  const target = updateCache().find((item) => item.bottom >= scroll);
  console.log(target);
  if (target) {
    anchorIndex = { ...target };
    startIndex.value = target.index;
    endIndex.value = target.index + visibleCount;
  } else {
    // updateCache();
    console.log("没有找到。。。");
  }
};

const handleScroll = (e: any) => {
  const currScrollTop = e.target.scrollTop;
  if (currScrollTop > preScrollTop) {
    // 向下滚动
    if (currScrollTop > anchorIndex.bottom) {
      preScrollTop = currScrollTop;
      updateIndex(currScrollTop);
      updateVisibleData();
    }
  }
};

const setItemRefs = (el: any) => {
  if (itemRefs.length > visibleCount) {
    itemRefs.shift();
  }
  itemRefs.push(el);
}
</script>

<template>
  <main class="main" ref="mainRef" @scroll="handleScroll">
    <div
      :style="{
        paddingTop: `${state.startOffset}px`,
        paddingBottom: `${state.endOffset}px`,
      }"
    >
      <div
        v-for="(item, index) in state.visibleData"
        :ref="setItemRefs"
        :key="startIndex + index"
        class="item"
        :style="{ height: `${itemHeight}px`, lineHeight: `${itemHeight}px` }"
      >
        {{ startIndex + index }}
      </div>
    </div>
  </main>
</template>

<style scoped>
.main {
  width: 400px;
  height: 300px;
  background-color: pink;
  overflow: auto;
}
.main .item {
  color: black;
  text-align: center;
  border-bottom: 1px solid #ccc;
}
</style>

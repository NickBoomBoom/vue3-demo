<script setup lang="ts">
import { RouterLink, RouterView } from "vue-router";
import StackSwiperItem from "./components/StackSwiper/StackSwiperItem.vue";
import StackSwiper from "./components/StackSwiper/index.vue";
</script>

<template>
  <div>
    <h1>swiper 堆叠</h1>
    <div class="border">
      <StackSwiper>
        <StackSwiperItem
          v-for="(item, index) in 4"
          :key="index"
          class="w-50 h-50 bg-red-400 text-right border"
        >
          {{ index }}
        </StackSwiperItem>
      </StackSwiper>
      <div class="text-center">中间</div>
    </div>
  </div>
  <RouterView />
</template>

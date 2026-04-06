<!-- hide the footer from "cover" layout -->
<template>
  <Scroll />
  <footer class="absolute bottom-5 left-0 right-7 text-right">
    <SlideCurrentNo v-if="$nav.currentPage !== 1" />
    <!-- / -->
    <!-- <SlidesTotal /> -->
  </footer>
</template>

<script setup lang="ts">
import { useSlideContext } from "@slidev/client";

const { $nav } = useSlideContext();
</script>

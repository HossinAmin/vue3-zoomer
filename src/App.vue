<template>
  <div class="flex h-screen w-screen items-center justify-center">
    <ZoomMapWrapper class="h-[32rem] w-[32rem]" :src="red" />
  </div>
</template>

<script setup lang="ts">
import red from "./assets/images/red.jpg";
import "./assets/css/main.css";
import DragZoomImg from "./components/DragZoomImg.vue";
</script>

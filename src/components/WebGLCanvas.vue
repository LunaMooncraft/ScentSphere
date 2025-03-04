<script setup lang="ts">
import { onMounted, ref } from "vue"
import { initScene } from "@/webgl/debug"

const canvasRef = ref(null)

onMounted(() => {
  if (canvasRef.value) {
    initScene(canvasRef);
  }
})
</script>

<template>
  <canvas ref="canvasRef"></canvas>
</template>

<style>
canvas {
  display: block;
  width: 200px;
}
</style>

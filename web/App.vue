<template>
  <RouterView/>
</template>

<script setup>
import { provide } from 'vue'
import { RouterView } from 'vue-router'
import { useGUI } from '#web/common/gui.js'

provide('GUI_STATE', useGUI())
</script>

<style scoped lang="less">
:deep(.app-container) {
  box-sizing: border-box;
  max-width: 900px;
  min-height: 100vh;
  margin: 0 auto;
}
</style>
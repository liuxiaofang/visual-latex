<template>
  <div class="app">
    <h1>LaTeX公式编辑器</h1>
    <LatexEditor />
  </div>
</template>

<script setup lang="ts">
import LatexEditor from './components/LatexEditor.vue'
</script>

<style scoped>
.app {
  padding: 20px;
}

h1 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 30px;
}
</style>
<script setup lang="ts">
interface Props {
  num: string
  type: '@' | '#' | '^' | '*'
}
const {num, type} = defineProps<Props>()
</script>

<template>
  <div class="card" :class="{color: type === '#' || type === '*'}">
    <h2>{{ num }}</h2>
    <h3>{{ type }}</h3>
  </div>
</template>

<style scoped>
.card {
  width: 100px;
  height: 150px;
  background-color: #fff;
  border-radius: 10px;
  color: black;
  border: 1px solid gainsboro;
  position: absolute;
  display: flex;
  box-sizing: border-box;
  padding: 0;
}
.color {
  color: red;
}
</style>

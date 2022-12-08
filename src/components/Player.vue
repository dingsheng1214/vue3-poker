<script setup lang="ts">
import Poker from './Poker.vue'
import {Poker as PokerType} from '../global'

const {pokers = [], direction = 'vertical'} = defineProps<{
  pokers: PokerType[]
  direction?: 'vertical' | 'hor'
}>()

const transformStyle = (index: number) => {
  if (direction === 'vertical') return `translateX(${50 * index}px)`
  if (direction === 'hor') return `translateY(${40 * index}px)`
}

console.log(pokers)
</script>

<template>
  <div>
    <Poker
      v-for="(poker, index) in pokers"
      :num="poker.num"
      :type="poker.type"
      :key="index"
      :class="{selected: poker.selected === true}"
      :style="{
        zIndex: index,
        transform: transformStyle(index),
      }"
    />
  </div>
</template>

<style scoped>
.selected {
  background-color: gold;
}
</style>

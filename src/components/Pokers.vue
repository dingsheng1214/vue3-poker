<script setup lang="ts">
import Poker from './Poker.vue'
import {Poker as PokerType} from '../global'

const {pokers} = defineProps<{pokers: PokerType[]}>()
</script>

<template>
  <div class="pokers">
    <Poker
      v-for="(poker, index) in pokers"
      :num="poker.num"
      :type="poker.type"
      :key="poker.num + poker.type"
      :style="{
        zIndex: index,
        transform: `translate(${-0.5 * index}px, ${-0.5 * index}px)`,
      }"
    />
  </div>
</template>

<style scoped>
.pokers {
  flex-grow: 1;
  flex-shrink: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>

<script setup lang="ts">
import Pokers from './components/Pokers.vue'
import Player from './components/Player.vue'
import {ref, unref} from 'vue'
import {Poker as PokerType} from './global'

interface PlayerType {
  pokers: PokerType[]
  calcProp: Record<string, PokerType[]>
  max?: [string, PokerType[]]
}
const nums = ['3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A', '2']
const types: PokerType['type'][] = ['@', '#', '^', '*']
const refresh = () => {
  const initPokers: PokerType[] = []
  nums.forEach((num) => {
    types.forEach((type) => {
      initPokers.push({num, type})
    })
  })
  return initPokers
}
const pokers = ref<PokerType[]>([...refresh()])
const player1 = ref<PlayerType>({pokers: [], calcProp: {}})
const player2 = ref<PlayerType>({pokers: [], calcProp: {}})
const player3 = ref<PlayerType>({pokers: [], calcProp: {}})
const player4 = ref<PlayerType>({pokers: [], calcProp: {}})

// 洗牌
const shuffle = () => {
  player1.value = {pokers: [], calcProp: {}}
  player2.value = {pokers: [], calcProp: {}}
  player3.value = {pokers: [], calcProp: {}}
  player4.value = {pokers: [], calcProp: {}}
  const a = [...refresh()]

  for (let i = a.length; i; i--) {
    let j = Math.floor(Math.random() * i)
    ;[a[i - 1], a[j]] = [a[j], a[i - 1]]
  }

  pokers.value = [...a]
}

// 发牌
const deal = () => {
  if (!pokers.value.length) return
  const times = refresh().length
  for (let i = 0; i < times; i++) {
    const poker = pokers.value.pop()!
    const playerIndex = i % 4
    if (playerIndex === 0) player1.value.pokers.push(poker)
    if (playerIndex === 1) player2.value.pokers.push(poker)
    if (playerIndex === 2) player3.value.pokers.push(poker)
    if (playerIndex === 3) player4.value.pokers.push(poker)
  }

  pokerSortAndCalc(unref(player1))
  pokerSortAndCalc(unref(player2))
  pokerSortAndCalc(unref(player3))
  pokerSortAndCalc(unref(player4))

  calc()
}

// 找出赢家
const calc = () => {
  const arr = [unref(player1), unref(player2), unref(player3), unref(player4)]
  arr.sort((a, b) => {
    const a1: PokerType[] = a.max![1]
    const b1: PokerType[] = b.max![1]
    if (a1.length === b1.length) {
      const a0 = a.max![0]
      const b0 = b.max![0]
      if (a0 === b0) {
        // [k@,k*] > [k#, k^]
        const a1_type = a1.sort((item1, item2) => {
          return (
            types.findIndex((item) => item === item2.type) -
            types.findIndex((item) => item === item1.type)
          )
        })[0].type

        const b1_type = b1.sort((item1, item2) => {
          return (
            types.findIndex((item) => item === item2.type) -
            types.findIndex((item) => item === item1.type)
          )
        })[0].type
        return (
          types.findIndex((item) => item === b1_type) -
          types.findIndex((item) => item === a1_type)
        )
      } else {
        // [k@,k#,k*] < [2@, 2#,2*]
        return (
          nums.findIndex((item) => item === b0) -
          nums.findIndex((item) => item === a0)
        )
      }
    } else {
      // [k@,k#,k*,k^] > [2@, 2#,2*]
      return b1.length - a1.length
    }
  })
  console.log('arr', arr)

  arr[0].max![1].forEach((item) => (item.selected = true))
}

// 玩家手中牌排序
const pokerSortAndCalc = (player: PlayerType) => {
  player.pokers.sort((poker1, poker2) => {
    const poker1Index = indexArr(poker1)
    const poker2Index = indexArr(poker2)

    if (poker1Index[0] === poker2Index[0]) {
      return poker1Index[1] - poker2Index[1]
    } else {
      return poker1Index[0] - poker2Index[0]
    }
  })

  const calcProp = player.pokers.reduce<PlayerType['calcProp']>(
    (prev, curr) => {
      if (prev.hasOwnProperty(curr.num)) {
        prev[curr.num].push(curr)
      } else {
        prev[curr.num] = [curr]
      }
      return prev
    },
    {}
  )

  const result = Object.entries(calcProp).sort((a, b) => {
    if (a[1].length !== b[1].length) return b[1].length - a[1].length
    else {
      const a0_index = nums.findIndex((item) => item === a[0])
      const b0_index = nums.findIndex((item) => item === b[0])
      return b0_index - a0_index
    }
  })
  player.calcProp = calcProp
  player.max = result[0]

  console.log(player.max)
}
const indexArr = ({num, type}: PokerType) => {
  const numIndex = nums.findIndex((item) => item === num)
  const typeIndex = types.findIndex((item) => item === type)
  return [numIndex, typeIndex]
}
</script>

<template>
  <div class="container">
    <div class="player1">
      <p>玩家1</p>
      <Player :pokers="player1.pokers" />
    </div>
    <div class="player2">
      <p>玩家2</p>
      <Player :pokers="player2.pokers" direction="hor" />
    </div>
    <div class="player3">
      <p>玩家3</p>
      <Player :pokers="player3.pokers" />
    </div>
    <div class="player4">
      <p>玩家4</p>
      <Player :pokers="player4.pokers" direction="hor" />
    </div>
    <div class="center">
      <div>
        <button @click="shuffle">洗牌</button>
        <button @click="deal">发牌</button>
      </div>
      <Pokers :pokers="pokers" />
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 100vw;
  height: 100vh;
  background-color: #2d57f7;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: repeat(5, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
}
.center {
  grid-area: 2 / 2 / 5 / 5;
  display: flex;
  flex-direction: column;
}
.player1 {
  grid-area: 1 / 2 / 2 / 5;
}
.player2 {
  grid-area: 2 / 5 / 5 / 6;
}
.player3 {
  grid-area: 5 / 2 / 6 / 5;
}
.player4 {
  grid-area: 2 / 1 / 5 / 2;
}
</style>

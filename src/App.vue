<script setup lang="ts">
import { ref, computed } from 'vue'
import GridItem from './components/grid-item.vue'
const row = ref(10)
const column = ref(10)
const bombNumber = ref(10)
const isGameOver = ref(false)
const grid: any = ref(null)
const gridItems = ref()
const total = computed(() => {
  return row.value * column.value
})

const StartGame = (event: any) => {
  const arr: any[] = []
  for (let i = 0; i < total.value; i++) {
    arr.push(0)
  }
  // 方法1
  arr.fill(1, 0, bombNumber.value)
  arr.sort(() => (Math.random() > 0.5 ? 1 : -1))
  // 方法2
  // let bomb = bombNumber.value
  // while (bomb) {
  //   const index = (Math.random() * total.value) >> 0
  //   if (arr[index]) {
  //     continue
  //   } else {
  //     arr[index] = 1
  //     bomb--
  //   }
  // }
  // --

  grid.value = arr.map((item, index) => {
    const x = index % column.value
    const y = (index / column.value) >> 0
    let count = 0
    for (let i = Math.max(0, y - 1); i < Math.min(y + 2, row.value); i++) {
      for (let j = Math.max(0, x - 1); j < Math.min(x + 2, column.value); j++) {
        if (arr[i * column.value + j] && !(i === y && j === x)) {
          count++
        }
      }
    }
    return {
      count,
      isBomb: !!item,
    }
  })
  if (event) {
    for (const item of gridItems.value) {
      item.reset()
    }
  }
  isGameOver.value = false
}
const Stop = () => {
  isGameOver.value = true
}
const onItemOpen = (item: any, index: any) => {
  if (grid.value[index].isBomb) {
    alert('game over')
    return Stop()
  }
  openGridItem(item, index)
}
const onItemFlag = (item: any, index: any) => {}
const openGridItem = (item: any, index: any) => {
  if (item.count) {
    return
  }
  const x = index % column.value
  const y = (index / column.value) >> 0
  for (let i = Math.max(0, y - 1); i < Math.min(y + 2, row.value); i++) {
    for (let j = Math.max(0, x - 1); j < Math.min(x + 2, column.value); j++) {
      if (i === y && j === x) {
        continue
      }
      const gridItem = gridItems.value[i * column.value + j]
      gridItem.open()
    }
  }
}
StartGame(event)
</script>

<template>
  <header style="margin-bottom: 1rem">
    <button type="button" @click="StartGame">⚔重置</button>
  </header>
  <div id="game-grid" :class="{ 'game-over': isGameOver }">
    <grid-item
      v-for="(item, index) in grid"
      class="grid-item"
      ref="gridItems"
      :count="item.count"
      :isBomb="item.isBomb"
      @open="onItemOpen(item, index)"
      @flag="onItemFlag(item, index)">
      {{ item ? '✨' : '' }}
    </grid-item>
  </div>
</template>

<style scoped></style>

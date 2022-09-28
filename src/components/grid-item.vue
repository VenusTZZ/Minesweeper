<script lang="ts" setup>
import { toRefs, ref } from 'vue'
const emit = defineEmits(['flag', 'open', 'openAll'])
const props = defineProps({
  count: Number,
  isBomb: Boolean,
})
const { count, isBomb } = toRefs(props)
const isOpen = ref(false)
const isFlag = ref(false)
const onclick = () => {
  if (isOpen.value || isFlag.value) {
    return
  }
  isOpen.value = true
  emit('open')
}
const onRightClick = () => {
  event?.preventDefault()
  if (isOpen.value) {
    return
  }
  isFlag.value = !isFlag.value
  emit('flag', isFlag.value)
}
const reset = () => {
  isOpen.value = false
  isFlag.value = false
}
const open = () => {
  if (isOpen.value || isFlag.value) {
    return
  }
  isOpen.value = true
  emit('open')
}
defineExpose({
  reset,
  open,
})
</script>
<template>
  <div
    class="grid-item"
    @click="onclick"
    :class="{ opened: isOpen }"
    @contextmenu="onRightClick">
    <template v-if="isFlag">🤣</template>
    <template v-else-if="isOpen">
      <template v-if="isBomb">✨</template>
      <template v-else="isBomb">{{ count ? count : '' }}</template>
    </template>
  </div>
</template>

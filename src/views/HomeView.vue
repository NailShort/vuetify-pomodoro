<template lang="pug">
v-row#home
  v-col(cols="7")(style="background:rgba(20,40,65,0.8);border-radius: 20px 20px 0 0 ; ")
    h1 {{ currentText }}
    h1 {{ currentTime }}
  v-col(cols="7")(style="background:rgba(20,40,65,0.8);border-radius: 0 0 20px 20px; ")
    v-btn(v-if="status !== 1" icon="mdi-play" variant="text" @click="startTimer")
    v-btn(v-if="status === 1" icon="mdi-pause" variant="text" @click="pauseTimer")
    v-btn(v-if="currentItem.length > 0" icon="mdi-skip-next" variant="text" @click="finishTimer")
</template>

<script setup>
import { ref, computed } from 'vue'
import { useListStore } from '@/stores/list'
import { useSettingsStore } from '@/stores/settings'
import { storeToRefs } from 'pinia'

const list = useListStore()
const { currentItem, items, timeleft } = storeToRefs(list)
const { start, countdown, finish } = list

const settings = useSettingsStore()
const { selectedAlarmFile, notify } = storeToRefs(settings)

// 0 = 停止
// 1 = 倒數中
// 2 = 暫停
const status = ref(0)

let timer = 0
const startTimer = () => {
  if (status.value === 0 && items.value.length > 0) {
    start()
  }
  if (currentItem.value.length > 0) {
    status.value = 1
    timer = setInterval(() => {
      countdown()
      if (timeleft.value === 0) {
        finishTimer()
      }
    }, 1000)
  }
}
const pauseTimer = () => {
  status.value = 2
  clearInterval(timer)
}
const finishTimer = () => {
  clearInterval(timer)
  status.value = 0
  const audio = new Audio()
  audio.src = selectedAlarmFile.value
  audio.play()
  if (notify.value) {
    // eslint-disable-next-line
    const notification = new Notification('事項完成', {
      body: currentText.value,
      icon: 'https://github.com/wdaweb.png'
    })
  }
  finish()
  if (items.value.length > 0) {
    startTimer()
  }
}

const currentText = computed(() => {
  return currentItem.value.length > 0 ? currentItem.value : items.value.length > 0 ? '點擊開始' : '沒有事項'
})
const currentTime = computed(() => {
  const m = Math.floor(timeleft.value / 60).toString().padStart(2, '0')
  const s = (timeleft.value % 60).toString().padStart(2, '0')
  return m + ':' + s
})
</script>

<style lang="scss">
  .v-main{
    position: relative;
  }
  .v-container{
    position: absolute;
    top:50%;
    left: 50%;
    transform: translate(-50%,-50%);
    text-align: center;
  }

  .v-col{
    margin: auto;

  }

  h1{
    color: white;
  }

  .v-col button{
  color: white;
}

</style>

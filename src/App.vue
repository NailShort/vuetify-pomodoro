<template lang="pug">
v-app
  v-app-bar
    v-app-bar-title Pomodoro
    v-spacer
    v-btn(icon="mdi-home" variant="text" to="/")
    v-btn(icon="mdi-format-list-bulleted" variant="text" to="/list")
    v-btn(icon="mdi-cog" variant="text" to="/settings")
    v-btn(:icon="notify ? 'mdi-bell' : 'mdi-bell-off'" variant="text" @click="toggleNotify")
  v-main
    v-container
      router-view(v-slot="{ Component }")
        //- 換頁保留元件不被銷毀
        //- 設定 include 指定要保留的元件
        keep-alive(include="HomeView")
          //- 動態元件，將元件以 is 傳入
          component(:is="Component")
</template>

<script setup>
import { useSettingsStore } from '@/stores/settings'
import { storeToRefs } from 'pinia'
const settings = useSettingsStore()
const { notify } = storeToRefs(settings)
const { toggleNotify } = settings
</script>

<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Rowdies:wght@300;400;700&display=swap');
.v-main{
  background: url(./images/bg.jpg) no-repeat center/cover;
  background-position-y: 80%;
}
.v-toolbar{
  height: 130px;
  background: url(./images/head-01.png) no-repeat center/cover;
  background-position-y: 80%;
  box-shadow: none;
}

.v-app-bar.v-toolbar:not(.v-toolbar--flat) {
  box-shadow:none;
}

.v-toolbar-title {
  font-size: 40px;
  color: rgb(65,110,175);
  font-family: 'Rowdies', cursive;
  line-height: 31px;
  text-indent: 10px;
}

.v-toolbar-title__placeholder{
  height: 10px;
}

.v-toolbar-title  {
  border-left: 5px solid rgb(20,40,65);
}

.v-toolbar__content a,button{
  color: rgb(65,110,175);
}

</style>

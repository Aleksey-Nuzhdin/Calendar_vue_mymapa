<template>
  <div id="app">
    <Calendar/>
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    Calendar
  }
}
</script>

<style lang="scss">
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app{
  width: 100vw;
  height: 100vh;
}
ul,li{
   list-style-type: none;
}
</style>

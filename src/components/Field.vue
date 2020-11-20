<template>
  <div class="field" id='scroll'>
    <div class="filed__wrap"
      :style="{ top:scroll+'px' }"
    >
      <ul class="time__list">
        <li class="hour">01:00</li>
        <li class="hour">02:00</li>
        <li class="hour">03:00</li>
        <li class="hour">04:00</li>
        <li class="hour">05:00</li>
        <li class="hour">06:00</li>
        <li class="hour">07:00</li>
        <li class="hour">08:00</li>
        <li class="hour">09:00</li>
        <li class="hour">10:00</li>
        <li class="hour">11:00</li>
        <li class="hour">12:00</li>
        <li class="hour">13:00</li>
        <li class="hour">14:00</li>
        <li class="hour">15:00</li>
      </ul>
      <ul class="day__list">
        <li class="day__item"></li>
        <li class="day__item"></li>
        <li class="day__item"></li>
        <li class="day__item"></li>
        <li class="day__item"></li>
        <li class="day__item"></li>
        <li class="day__item"></li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Field',
  props:['scroll','from','to'],
  data:()=>({
    setFrom: 0,
    setTo: 0,
  }),
  methods:{
    setDate(){
      this.setFrom = +this.from
      this.setTo = +this.to
    },
  },
  created(){
    this.$parent.$on('setDate', this.setDate);
  },
}
</script>

<style scoped lang="scss">
.field{
  position: relative;
  width: 100%;
  max-height: calc(100vh - 300px);
  min-height: 300px;
  height: 70vh;
}
.filed__wrap{
  position: absolute;
  display: flex;
  top: 0;
}
.time__list{
  padding-top: 24px;
  height: 100%;
  width: 80px;
  color: #717171;
}
.hour{
  position: relative;
  display: flex;
  justify-content: right;
  align-items: center;
  height: 50px;
  width: 80px;
  padding-right: 18px;
}
.day__list{
  position: relative;
  display: flex;
  &:after{
    content: "";
    display: block;
    position: absolute;
    width: 13px;
    height: 100%;
    left: -13px;
    background-image: linear-gradient(white 48px, #d6d4d4 2px);
    background-size: 200px 50px;
    background-repeat: repeat;
  
  }
  
}
.day__item{
  height: 100%;
  width: 200px;
  border-left: 2px solid #d6d4d4;
  background-image: linear-gradient(white 48px, #d6d4d4 2px);
  background-size: 200px 50px;
  background-repeat: repeat;

}

</style>
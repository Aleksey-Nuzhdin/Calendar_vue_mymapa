<template>
  <div class="wrapper">
    <h1 class="title">Календарь</h1>
    <div class="calendar">
      <ul class="date__list">
        <div class="gmt"><span>GMT+03</span></div> 
        <li class="date__item">
          <span class="day_week">ПН</span>
          <span class="number">19</span>
        </li>
        <li class="date__item">
          <span class="day_week">ВТ</span>
          <span class="number">20</span>
        </li>
        <li class="date__item">
          <span class="day_week">СР</span>
          <span class="number">21</span>
        </li>
        <li class="date__item">
          <span class="day_week">СР</span>
          <span class="number">21</span>
        </li>
        <li class="date__item">
          <span class="day_week">СР</span>
          <span class="number">21</span>
        </li>
        <li class="date__item">
          <span class="day_week">СР</span>
          <span class="number">21</span>
        </li>
        <li class="date__item">
          <span class="day_week">СР</span>
          <span class="number">21</span>
        </li>
      </ul>
      <Field />
    </div>
  </div>
</template>

<script>
import Field from './Field'

export default {
  name: 'Calendar',
  methods:{

  },
  components:{ Field, }
}
</script>


<style scoped lang="scss">
.wrapper{
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  padding: 40px;
}
.title{
  grid-area: title;
  text-align: center;
  width: 100%;
  height: 80px;
}
.calendar{
  display: flex;
  flex-wrap: wrap;
  overflow-x: auto;
  overflow-y: hidden;

  width: 100%;
  min-height: 200px;
}
.gmt{
  height: 100%;
  width: 80px;
  border-bottom: 2px solid #d6d4d4;
  
  display: flex;
  justify-content: right;
  align-items: flex-end;
  padding-right: 18px;

  & span{
    width: 80px;
  }
}

.date__list{
  display: flex;
  z-index: 2;
  background-color: white;
  height: 80px;
  width: 100%;
}
.date__item{
  display: flex;
  background-color: white;
  flex-direction: column;
  justify-content: space-around;
  width: 200px;
  height: 100%;
  text-align: center;
  border-bottom: 2px solid #d6d4d4;

  background-image: linear-gradient(white, #d6d4d4);
  background-size: 2px 30px;
  background-repeat: no-repeat;
  background-position: 0 100%;
  color: #717171;
}
.day_week{
  font-size: 18px;
}
.number{
  width: 200px;
  font-size: 40px;
}

</style>

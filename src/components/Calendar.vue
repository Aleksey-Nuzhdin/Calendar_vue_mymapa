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
          <span class="day_week">{{dateFilrer(toDo[3].startDate,{weekday:'short'}).toUpperCase()}}</span>
          <span class="number">{{dateFilrer(toDo[3].startDate,{day:'2-digit'})}}</span>
        </li>
      </ul>
      <Field 
        :scroll='scroll'
        :from='from'
        :to='to'
      />
      <a @click.prevent='scrollUp' class="scrollBtn scrollUp">Up</a>
      <a @click.prevent='scrollDown' class="scrollBtn scrollDown">Down</a> 
    </div>
    <form class="setDate"
        @submit.prevent='submit'
      >
        <label for="from">От</label>
        <input
            id="from"
            type="text"
            v-model="from"
        >
        
        <label for="to">До</label>
        <input
            id="to"
            type="text"
            v-model="to"
        >
        <button
            class=""
            type="submit"
        >
          Показать
        </button>
      </form>
  </div>
</template>

<script>
import Field from './Field'

export default {
  name: 'Calendar',
  data:()=>({
    scroll: 0,
    from: 0,
    to: 0,
    toDo:[
          {"title": "Заголовок 1", "startDate": 1585699200, "endDate": 1585702800},
          {"title": "Заголовок 2", "startDate": 1585706400, "endDate": 1585710000},
          {"title": "Заголовок 3", "startDate": 1585789200, "endDate": 1585792800},
          {"title": "Заголовок 4", "startDate": 1586307600, "endDate": 1586311200},
          {"title": "Заголовок 5", "startDate": 1585791500, "endDate": 1585795100},
          {"title": "Заголовок 6", "startDate": 1585706400, "endDate": 1585710000}
        ]

  }),
  methods:{
    scrollUp(){
      this.scroll += 50
    },
    scrollDown(){
      this.scroll -= 50
    },
    submit(){
      this.$emit('setDate')
    },
    dateFilrer(val, options, lang='ru-RU'){
      const date = new Date(val*1000)
      return new Intl.DateTimeFormat(lang, options).format(date)
    }
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
  display: flex;
  justify-content: right;
  align-items: flex-end;
  padding-right: 18px;

  background-image: linear-gradient(to right, white, #d6d4d4);
  background-size: 80px 2px;
  background-repeat: no-repeat;
  background-position: 100% 100%;

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
  color: #717171;
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

.scrollBtn{
  position: absolute;
  display: block;
  height: 40px;
  width: 40px;
  border-radius: 50%;
  background-color: rgb(7, 190, 68);
}
.scrollUp{
  right: 50px;
  bottom: 150px;
}
.scrollDown{
  right: 50px;
  bottom: 100px;
}

</style>

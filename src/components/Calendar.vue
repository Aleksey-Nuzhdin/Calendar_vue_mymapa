<template>
  <div class="wrapper">
    <h1 class="title">Календарь</h1>
    <div class="calendar">
      <ul class="date__list">
        <div class="gmt"><span>GMT+03</span></div> 
        <li class="date__item"
          v-for="(day, index) in dayList" :key="index"
        >
          <span class="day_week">{{day.weekDay.toUpperCase()}}</span>
          <span class="number">{{day.day}}</span>
        </li>
      </ul>
      <Field 
        :from='from'
        :to='to'
        :dayList='dayList'
        :toDo='toDoList'
      />
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
        <button
            @click="setDateExaple"
        >
          Задание
        </button>
  </div>
</template>

<script>
import Field from './Field'

export default {
  name: 'Calendar',
  data:()=>({
    from: 0,
    to: 0,
    toDo:[
          {"title": "Заголовок 1", "startDate": 1585699200, "endDate": 1585702800},
          {"title": "Заголовок 2", "startDate": 1585706400, "endDate": 1585710000},
          {"title": "Заголовок 3", "startDate": 1585789200, "endDate": 1585792800},
          {"title": "Заголовок 4", "startDate": 1586307600, "endDate": 1586311200},
          {"title": "Заголовок 5", "startDate": 1585791500, "endDate": 1585795100},
          {"title": "Заголовок 6", "startDate": 1585706400, "endDate": 1585710000}
        ],
    dayList:[],
    toDoList:[],

  }),
  methods:{
    submit(){
      this.setDayList()
      this.$emit('setDate')
    },
    setDateExaple(){
      this.from = 1585699200000
      this.to = 1585699200000 + (7*24*60*60*1000)
      this.setDayList()
      this.setToDoList()
    },
    dateFilrer(val, options, lang='ru-RU'){
      const date = new Date(val)
      return new Intl.DateTimeFormat(lang, options).format(date)
    },
    setDayList(){
      const fromDate = new Date(this.from)
      const toDate = new Date(this.to)
      let fromSec =  Math.floor(this.from/1000)
      this.dayList =[]
      
      while(true){
        let weekDay = this.dateFilrer(fromDate, {weekday:'short'})
        let day = this.dateFilrer(fromDate, {day:'2-digit'})
        this.dayList.push({
          day,
          weekDay,
          from: fromSec,
          to: fromSec + (24*60*60),
        })
        if(fromDate.getDate() === toDate.getDate() && fromDate.getMonth() === toDate.getMonth()) break
        fromDate.setDate(fromDate.getDate() + 1)
        fromSec+=(24*60*60)
      }
    },
    setToDoList(){
      console.log('h');
      this.toDoList = this.dayList.map((eList, inx)=>{ console.log(1); return (
          this.toDo.filter((eDo)=>{
            //Исправляем, если вермя начала позже времяени окончания
            if(eDo.startDate > eDo.endDate){
              let val = eDo.startDate
              eDo.startDate = eDo.endDate
              eDo.endDate = val
            }
            console.log(eDo.startDate, eList.from, eDo.endDate, eList.to);
            if(eDo.startDate >= eList.from && eDo.endDate <= eList.to) return eDo
        }))
      })
    },
    correctionDateDay(date){
      //Поулчает UTC региона, для корекктного отображения
      const UTC = this.UTC

      //Приравнеием время к 00:00
      return Math.floor((date) /(24*60*60*1000)) * (24*60*60*1000) + UTC + 1
    },
  },
  created(){   
    //По умолчанию показывать 7 дней начиная с сегодняшнего
    this.from = this.correctionDateDay(Date.now())
    this.to = this.from + (7*24*60*60*1000) - 1
    this.setDayList()
    this.setToDoList()
    
  },
  computed:{
    UTC:() => ((new Date).getTimezoneOffset() * 60 * 1000),
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




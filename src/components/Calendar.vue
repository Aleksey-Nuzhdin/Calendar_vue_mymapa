<template>
  <div class="wrapper">
    <h1 class="title">Календарь</h1>
    <div class="calendar">
      <ul class="date__list">
        <div class="gmt"><span>GMT{{GMT}}</span></div> 
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
        :setToDoList='setToDoList'
      />
    </div>
    <form class="setDate"
      @submit.prevent='submit'
    >
      <div class="setDate__wrpa">
        <div class="form_tilt">ОТ</div>
        <div class="input__wrap"
          v-for="(item, key) of fromDate" :key="key"
        >
          <label for="from">{{key}}</label>
          <input
            :id='key'
            class="input_date"
            type="text"
            
            v-model="fromDate[key]"
          >
        </div>
      </div>
      <div class="setDate__wrpa">
        <div class="form_tilt">ДО</div>
        <div class="input__wrap"
          v-for="(item, key) of toDate" :key="item"
        >
          <label for="from">{{key}}</label>
          <input
            :id='key'
            class="input_date"
            type="text"
            
            v-model="toDate[key]"
          >
        </div>
      </div>
      
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
    fromDate:{year:2020, month:11, day:0},
    toDate:{year:2020, month:11, day:0},
    from: 0,
    to: 0,
    GMT: '00',
    toDo:[
          {"title": "Заголовок 1", "startDate": 1585699200, "endDate": 1585703800},
          {"title": "Заголовок 3", "startDate": 1585789200, "endDate": 1585792800},
          {"title": "Заголовок 4", "startDate": 1586307600, "endDate": 1586315200},
          {"title": "Заголовок 5", "startDate": 1585791500, "endDate": 1585795100},
          {"title": "Заголовок 2", "startDate": 1585706400, "endDate": 1585710000},
          {"title": "Заголовок 6", "startDate": 1585706400, "endDate": 1585710000}
        ],
    dayList:[],
    toDoList:[],

  }),
  methods:{
    submit(){
      const from = new Date(this.fromDate.year, this.fromDate.month-1, this.fromDate.day)
      const to = new Date(this.toDate.year, this.toDate.month-1, this.toDate.day)
      this.from = from.valueOf()
      this.to = to.valueOf()
      
      if(this.from > this.to){
        let val = this.from
        this.from = this.to
        this.to = val
      }
 
      this.setDayList()
      this.setToDoList()
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
      this.dayList = []
      
      //Создаём массив с обектами для отображения даты и дня недели
      while(true){
        let weekDay = this.dateFilrer(fromDate, {weekday:'short'})
        let day = this.dateFilrer(fromDate, {day:'2-digit'})
        this.dayList.push({
          day,
          weekDay,
          from: fromSec + (this.UTC/1000),
          to: fromSec + (24*60*60) + (this.UTC/1000),
        })
        if(fromDate.getDate() === toDate.getDate() && fromDate.getMonth() === toDate.getMonth() && fromDate.getFullYear() === toDate.getFullYear() ) break
        fromDate.setDate(fromDate.getDate() + 1)
        fromSec+=(24*60*60)
      }
    },
    setToDoList(){
      //Распределяем "дела" по дням их выполнения
      const mass = this.toDo.map(el => {return {...el, parent: el}})
      this.toDoList = this.dayList.map((eList, inx)=>{ return (
          mass.filter((eDo)=>{
            //Исправляем, если вермя начала позже времяени окончания
            if(eDo.startDate > eDo.endDate){
              let val = eDo.startDate
              eDo.startDate = eDo.endDate
              eDo.endDate = val
            }
            //Возвращаем "дела", время которых выпадает на нужный день
            
            if(eDo.startDate >= eList.from && eDo.endDate <= eList.to){
              return eDo
            } 
        }))
      })
      //Модифицируем для отображения
      this.toDoList = this.toDoList.map((arr)=>{return (arr.map((el)=>{
        if(arr.length > 0){
          
          //коэффициент сколько времени прошло от 00:00 до времени начала "дела"
          el.coefTop = ( (el.startDate - (this.UTC/1000) ) % (24*60*60))/(24*60*60)
          //коэффициент сколько времени длилось "дело"
          el.coefHeight = (el.endDate - el.startDate)/(24*60*60)
          //время в часах и минутах
          el.timeStart = this.dateFilrer(el.startDate*1000, {hour:'2-digit',minute:'2-digit'})
          el.timeEnd = this.dateFilrer(el.endDate*1000, {hour:'2-digit',minute:'2-digit'})
        }
        return el
      }))})
      //Если 2 события в одно время задаём смещение
      for(let i = 0; i < this.toDoList.length; i++){

        let arr = this.toDoList[i]
        for(let j = 0; j < arr.length; j++){

          arr[j].coefLeft = 0
          for(let k = j+1; k < arr.length; k++){
                 
            if( 
              Math.abs(arr[j].startDate - arr[k].startDate) < 1200 && 
              Math.abs(arr[j].endDate - arr[k].endDate) < 1200
            ){   
              arr[j].coefLeft += 1
            }
          }
        }
      }
    },
    correctionDateDay(date, type = 'start'){
      //Приравнеием время к 00:00 или к 24:00
      if(type === 'start') return Math.floor((date - this.UTC) / (24*60*60*1000)) * (24*60*60*1000) + this.UTC
      if(type === 'end') return Math.floor((date - this.UTC) /(24*60*60*1000)) * (24*60*60*1000) + this.UTC + (24*60*60*1000) - 1
      Error('Wrong type');
    },
    setGMT(){
      if(this.UTC > 0){
      this.GMT = '0'+(this.UTC / (60*1000*60))
      this.GMT = this.GMT.slice(-2)
      this.GMT ='-'+this.GMT
      }else{
      this.GMT = '0'+(-this.UTC / (60*1000*60))
      this.GMT = this.GMT.slice(-2)
      this.GMT ='+'+this.GMT
     }
    },
  },
  created(){   
    this.setGMT()
    //По умолчанию показывать 7 дней начиная с сегодняшнего
    this.from = this.correctionDateDay(Date.now())
    this.to = this.from + (7*24*60*60*1000) - 1
    this.setDayList()
    this.setToDoList()
    
  },
  computed:{
    //UTC региона, для коррекции времени
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

.setDate{
  display: flex;
}
.setDate__wrap{
  display: block;
}
</style>




<template>
  <div class="field__wrap"
    @wheel.prevent="setScroll"
    ref="field__wrap"
  >
    <div class="field"
      :style="{ top:scroll+'px' }"
      ref="field"
    >
      <ul class="time__list">
        <li class="hour"
          v-for="time in timeArr" :key="time"
        >
        {{time}}:00</li>
      </ul>
      <ul class="day__list"
        
      >
        <li class="day__item"
          ref="day__item"
          v-for="(day, index) of dayList" :key="index"
          
          @dragenter.self="dragenter($event)" 
          @dragover.prevent=''
          @drop.prevent.self="drop($event)"
        >
          <div class="toDo__item"
            v-for="(item, ind) in toDo[index]" :key="ind"
            :style="{top:(item.coefTop * dayItemHeight)+'px', height:(item.coefHeight * dayItemHeight)+'px', left:(item.coefLeft)*30 + 'px' }"
            draggable="true"
            @dragstart.self="dragstart( item, $event,)"
            @dragend.prevent="dragend($event)"
            
           
          >
          {{item.title}}
          <div class="toDo__time"
            v-if="(item.coefHeight * dayItemHeight) > 50"
          >
            {{item.timeStart}}-{{item.timeEnd}}
          </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Field',
  props:['from','to','dayList','toDo','setToDoList'],
  data:()=>({
    setFrom: 0,
    setTo: 0,
    scroll: 0,
    fieldHeight: 0,
    fieldWrapHeight: 0,
    dayItemWidth: 0,
    dayItemHeight: 0,
    dragged: undefined,
    dragItem: {},
    timeArr: []
  }),
  methods:{
    setDate(){
      this.setFrom = +this.from
      this.setTo = +this.to
    },
    updataItem(top, dayIndex, item){
      let time = item.parent.endDate - item.parent.startDate
      item.parent.startDate = this.dayList[dayIndex].from + Math.floor((top / this.dayItemHeight)*(24*60*60))
      item.parent.endDate = item.parent.startDate + time

      //top = item.coefTop * dayItemHeight
    },
    setScroll(e){
      if(e.deltaY < 0){
        if((this.scroll - e.deltaY*10) > 0 ){
           this.scroll = 0
        }else{
           this.scroll -= e.deltaY*10
        }
      }else{
        if ((this.scroll - e.deltaY*10) < (this.fieldWrapHeight - this.fieldHeight)) {
          this.scroll = this.fieldWrapHeight - this.fieldHeight
        } else {
          this.scroll -= e.deltaY*10
        }     
      }  
    },
    dragstart(item, e){
      //console.log(e.target.offsetWidth, );

      e.dataTransfer.dropEffect = 'move'
      e.dataTransfer.effectAllowed = 'uninitialized'

      this.dragged = e.target
      this.dragItem = item

      //let shiftX = e.clientX - this.dragged.getBoundingClientRect().left;
      let shiftY = e.clientY - this.dragged.getBoundingClientRect().top;

      let startListIndex = Array.prototype.indexOf.call(
        e.target.offsetParent.offsetParent.children, 
        e.target.offsetParent)

      

      //e.dataTransfer.setData('startListIndex', startListIndex)
      //e.dataTransfer.setData('shiftX', shiftX)
      e.dataTransfer.setData('shiftY', shiftY)
      //e.dataTransfer.setData('width', e.target.offsetWidth)
      e.dataTransfer.setData('heightItem', e.target.offsetHeight)
      
      
      e.target.style.opacity = 0.5;
      
    },
    drop(e){
    
      let endListIndex = Array.prototype.indexOf.call(
        e.target.offsetParent.children, 
        e.target)
      
      let startListIndex = parseInt(e.dataTransfer.getData('startListIndex'))
      //let itemIndex = this.toDo[startListIndex].indexOf(this.dragItem)
      let moveY = e.layerY - parseInt(e.dataTransfer.getData('shiftY'))
      //let moveX = e.layerX - parseInt(e.dataTransfer.getData('shiftX'))
      //let widthItem = parseInt(e.dataTransfer.getData('width'))
      let heightItem = parseInt(e.dataTransfer.getData('heightItem'))
      //let width = e.target.offsetWidth
      let height = e.target.offsetHeight

      //Ограничиваем смещение
      //if(moveX < 0) moveX = 0
      //if(moveX > width-widthItem) moveX = (width-widthItem)

      if(moveY < 0) moveY = 0
      if(moveY > height - heightItem) moveY = height - heightItem
      
      //this.dragged.style.top = moveY +'px'
      //this.dragged.style.left = moveX +'px'

      this.updataItem(moveY, endListIndex, this.dragItem)
      this.setToDoList()
    
    },
    dragend(e){
      e.target.style.opacity = ''; 
    },
    dragenter(e){
      //console.log(e);
      
    }
  },
  created(){

    for(let i = 1; i < 24; i++){
      if(i < 10) this.timeArr.push('0'+i)
      else this.timeArr.push(i)
    }
  },
  mounted(){
    this.fieldHeight =  this.$refs.field.clientHeight;
    this.fieldWrapHeight =  this.$refs.field__wrap.clientHeight;
    this.dayItemWidth =  this.$refs.day__item[0].clientWidth;
    this.dayItemHeight =  this.$refs.day__item[0].clientHeight;
  }
}
</script>

<style scoped lang="scss">
.field__wrap{
  position: relative;
  width: 100%;
  max-height: calc(100vh - 400px);
  min-height: 300px;
  height: 1100px;
}
.field{
  position: absolute;
  display: flex;
  top: 0;
}
.time__list{
  padding-top: 24px;
  padding-bottom: 24px;
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
  position: relative;
  height: 100%;
  width: 200px;
  border-left: 2px solid #d6d4d4;
  background-image: linear-gradient(white 48px, #d6d4d4 2px);
  background-size: 200px 50px;
  background-repeat: repeat;

}
.toDo__item{
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  position: absolute;
  background-color: rgb(167, 230, 52);
  padding: 5px;
  color: #474646;
  border: 2px solid white;
  border-radius: 8px;
  cursor: pointer;
  user-select: none;
  max-width: 80%;
}
</style>
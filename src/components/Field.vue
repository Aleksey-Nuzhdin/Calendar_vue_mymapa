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
          @drop.prevent="drop($event)"
          
          @dragover.prevent='dragenter($event)'
        >
          <div class="toDo__item"
            v-for="(item, ind) in toDo[index]" :key="ind"
            :style="{top:(item.coefTop * dayItemHeight)+'px', height:(item.coefHeight * dayItemHeight)+'px' }"
            draggable="true"
            @dragstart="dragstart($event)"
            @dragend.prevent="dragend($event)"
            @dragenter.prevent
           
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
  props:['from','to','dayList','toDo'],
  data:()=>({
    setFrom: 0,
    setTo: 0,
    scroll: 0,
    fieldHeight: 0,
    fieldWrapHeight: 0,
    dayItemWidth: 0,
    dayItemHeight: 0,
    dragged: undefined,
    timeArr: []
  }),
  methods:{
    setDate(){
      this.setFrom = +this.from
      this.setTo = +this.to
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
    dragstart(e){
      //console.log(e);
      this.dragged = e.target
      //console.log(this.dragged, 11);
      e.target.style.opacity = 0.5;
      //e.dataTransfer.setData('text/plain', 'dummy');
    },
    drop(e){
      //console.log(1);
      console.log(e);
    },
    dragend(e){
      //console.log(e.screenY);
      //console.log(e.target.style.top);
      //console.log(this.toDo[0][0]);
      //this.toDo[0][0].coefTop+= 0.005
      e.target.style.opacity = ''; 
    },
    dragenter(e){
      //console.log(e);
      
    }
  },
  created(){
    //this.$parent.$on('setDate', this.setDate);

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
}
</style>
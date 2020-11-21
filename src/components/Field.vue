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
      <ul class="day__list">
        <li class="day__item"
          v-for="(day, index) in dayList" :key="index"
        >
          <div class="toDo__item"
            v-for="(item, ind) in toDo[index]" :key="ind"
          >
          {{item.title}}
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
    }
  },
  created(){
    this.$parent.$on('setDate', this.setDate);

    for(let i = 1; i < 24; i++){
      if(i < 10) this.timeArr.push('0'+i)
      else this.timeArr.push(i)
    }
  },
  mounted(){
    this.fieldHeight =  this.$refs.field.clientHeight;
    this.fieldWrapHeight =  this.$refs.field__wrap.clientHeight;
  }
}
</script>

<style scoped lang="scss">
.field__wrap{
  position: relative;
  width: 100%;
  max-height: calc(100vh - 300px);
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
  background-color: rgb(22, 105, 14);
}
</style>
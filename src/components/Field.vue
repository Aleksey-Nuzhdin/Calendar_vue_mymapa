<template>
  <div class="field__wrap" @wheel.prevent="setScroll" ref="field__wrap">
    <div class="field" :style="{ top: scroll + 'px' }" ref="field">
      <ul class="time__list">
        <li class="hour" v-for="time in timeArr" :key="time">{{ time }}:00</li>
      </ul>
      <ul class="day__list">
        <li
          class="day__item"
          ref="day__item"
          v-for="(day, index) of dayList"
          :key="index"
          @dragenter.self="dragenter($event)"
          @dragover.prevent=""
          @drop.prevent="drop($event)"
        >
          <div
            class="toDo__item"
            v-for="(item, ind) in toDo[index]"
            :key="ind"
            :style="{
              top: item.coefTop * dayItemHeight + 'px',
              minHeight: item.coefHeight * dayItemHeight + 'px',
              left: item.coefLeft * 30 + 'px',
            }"
            draggable="true"
            @dragstart.self="dragstart(item, $event)"
            @dragend.prevent="dragend($event)"
          >
            {{ item.title }}
            <span
              class="toDo__itemtext"
              @drop.prevent=""
              v-if="item.coefHeight * dayItemHeight > 50"
            >
              {{ item.timeStart }}-{{ item.timeEnd }}
            </span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Field",
  props: ["from", "to", "dayList", "toDo", "setToDoList"],
  data: () => ({
    setFrom: 0,
    setTo: 0,
    scroll: 0,
    fieldHeight: 0,
    fieldWrapHeight: 0,
    dayItemWidth: 0,
    dayItemHeight: 0,
    dragged: undefined,
    dragItem: {},
    timeArr: [],
  }),
  methods: {
    setDate() {
      this.setFrom = +this.from;
      this.setTo = +this.to;
    },
    updataItem(top, dayIndex, item) {
      //Обновляем значения времени начала и конца
      let time = item.parent.endDate - item.parent.startDate;
      item.parent.startDate =
        this.dayList[dayIndex].from +
        Math.floor((top / this.dayItemHeight) * (24 * 60 * 60));
      item.parent.endDate = item.parent.startDate + time;
    },
    setScroll(e) {
      if (e.deltaY < 0) {
        if (this.scroll - e.deltaY * 10 > 0) {
          this.scroll = 0;
        } else {
          this.scroll -= e.deltaY * 10;
        }
      } else {
        if (
          this.scroll - e.deltaY * 10 <
          this.fieldWrapHeight - this.fieldHeight
        ) {
          this.scroll = this.fieldWrapHeight - this.fieldHeight;
        } else {
          this.scroll -= e.deltaY * 10;
        }
      }
    },
    dragstart(item, e) {
      e.dataTransfer.dropEffect = "move";
      e.dataTransfer.effectAllowed = "uninitialized";

      this.dragged = e.target;
      this.dragItem = item;

      let shiftY = e.clientY - this.dragged.getBoundingClientRect().top;

      let startListIndex = Array.prototype.indexOf.call(
        e.target.offsetParent.offsetParent.children,
        e.target.offsetParent
      );

      //Перебрасываем значения
      e.dataTransfer.setData("shiftY", shiftY);
      e.dataTransfer.setData("heightItem", e.target.offsetHeight);

      e.target.style.opacity = 0.5;
    },
    drop(e) {
      let endListIndex;
      let height;

      //Если событие произошло на внутренних элементах
      if (e.target.className == "toDo__item") {
        endListIndex = Array.prototype.indexOf.call(
          e.target.offsetParent.offsetParent.children,
          e.target.offsetParent
        );

        height = e.target.offsetParent.offsetHeight;
      } else {
        endListIndex = Array.prototype.indexOf.call(
          e.target.offsetParent.children,
          e.target
        );

        height = e.target.offsetHeight;
      }

      if (e.target.className == "toDo__itemtext") {
        endListIndex = Array.prototype.indexOf.call(
          e.target.offsetParent.offsetParent.offsetParent.children,
          e.target.offsetParent.offsetParent
        );

        height = e.target.offsetParent.offsetParent.offsetHeight;
      }

      let moveY =
        e.pageY -
        this.scroll -
        parseInt(e.dataTransfer.getData("shiftY")) -
        200;
      let heightItem = parseInt(e.dataTransfer.getData("heightItem"));

      //Ограничиваем смещение

      if (moveY < 0) moveY = 0;
      if (moveY > height - heightItem) moveY = height - heightItem;

      //обновляем данные и перерисовываем
      this.updataItem(moveY, endListIndex, this.dragItem);
      this.setToDoList();
    },
    dragend(e) {
      e.target.style.opacity = "";
    },
    dragenter(e) {
      //console.log(e);
    },
  },
  created() {
    for (let i = 1; i < 24; i++) {
      if (i < 10) this.timeArr.push("0" + i);
      else this.timeArr.push(i);
    }
  },
  mounted() {
    this.fieldHeight = this.$refs.field.clientHeight;
    this.fieldWrapHeight = this.$refs.field__wrap.clientHeight;
    this.dayItemWidth = this.$refs.day__item[0].clientWidth;
    this.dayItemHeight = this.$refs.day__item[0].clientHeight;
  },
};
</script>

<style scoped lang="scss">
.field__wrap {
  position: relative;
  width: 100%;
  max-height: calc(100vh - 400px);
  min-height: 300px;
  height: 1100px;
}
.field {
  position: absolute;
  display: flex;
  top: 0;
}
.time__list {
  padding-top: 24px;
  padding-bottom: 24px;
  height: 100%;
  width: 80px;
  color: #717171;
}
.hour {
  position: relative;
  display: flex;
  justify-content: right;
  align-items: center;
  height: 50px;
  width: 80px;
  padding-right: 18px;
}
.day__list {
  position: relative;
  display: flex;
  &:after {
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
.day__item {
  position: relative;
  height: 100%;
  width: 200px;
  border-left: 2px solid #d6d4d4;
  background-image: linear-gradient(white 48px, #d6d4d4 2px);
  background-size: 200px 50px;
  background-repeat: repeat;
}
.toDo__item {
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
  text-overflow: clip;
}
</style>

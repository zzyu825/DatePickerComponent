<template>
    <div class="date-picker" v-click>
        <!-- 输入区域 -->
        <div class="picker-input">
            <span class="input-prefix">
                <i class="iconfont icon-rili"></i>
            </span>
            <input type="text" :value="dateFormat">
        </div>
        <!-- 选择区域 -->
        <div class="picker-panel" v-if="show">
            <div class="picker-arrow"/>
            <div class="picker-body">
                <div class="picker-header">
                    <span class="picker-btn icon-prev-year iconfont" @click="changeYear('prev')"></span>
                    <span class="picker-btn icon-prev-month iconfont" @click="changeMonth('prev')"></span>
                    <span class="picker-date">
                      {{ showDate.year }}年{{ showDate.month + 1 }}月
                    </span>
                    <span class="picker-btn icon-next-month iconfont" @click="changeMonth('next')"></span>
                    <span class="picker-btn icon-next-year iconfont" @click="changeYear('next')"></span>
                </div>
                <div class="picker-content">
                    <div class="picker-weeks">
                        <div 
                            v-for="week of ['日', '一', '二', '三', '四', '五', '六']"
                            :key="week"
                        >{{ week }}</div>
                    </div>
                    <div class="picker-days">
                        <div 
                            v-for="date of showDay"
                            :key="date.getTime()"
                            :class="{
                              'other-month': !isCur(date).month,
                              'is-today': isCur(date).day,
                              'is-select': isCur(date).select
                            }"
                            @click="chooseDate(date)"
                        >{{ date.getDate() }}</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  directives: {
    'click': {
      bind(el, binding, vnode) {
        document.onclick = e => {
          const target = e.target;
          const vm = vnode.context;
          // contains 包含
          const isInclude = el.contains(target);
          if (isInclude && !vm.show) {
            vm.showPanel(true);
          } else if (!isInclude && vm.show) {
            vm.showPanel(false);
          }
          // a真b假 a假b假 a真b真 a假b真
        }
      }
    }
  },
  props: {
    'date': {
      type: Date,
      default: () => new Date()
    }
  },
  model: {
    prop: 'date',
    event: 'choose-date'
  },
  data() {
    return {
      showDate: {
        year: 0,
        month: 0
      },
      show: false
    }
  },
  created () {
    this.showDate = this.getYearMonthDay(this.date);
  },
  computed: {
    dateFormat() {
      const { year, month, day } = this.getYearMonthDay(this.date);
      return `${year}-${month + 1}-${day}`;
    },
    showDay() {
      const { year, month } = this.showDate;
      const firstDay = new Date(year, month, 1);
      const week = firstDay.getDay();
      const startDay = firstDay - week * 24 * 60 * 60 * 1000;
      const arr = [];
      for (let i = 0; i < 42; i++) {
        arr.push(new Date(startDay + i * 24 * 60 * 60 * 1000));
      }
      return arr;
    }
  },
  methods: {
    getYearMonthDay(date) {
      const year = date.getFullYear();
      const month = date.getMonth();
      const day = date.getDate();
      return {
        year,
        month,
        day
      }
    },
    changeYear(type) {
      const num = type === 'prev' ? -1 : 1;
      this.showDate.year += num;
    },
    changeMonth(type) {
      let { year, month, day } = this.showDate;
      const num = type === 'prev' ? -1 : 1;
      // if (month !== 'undefined') {
      //   month += num;
      //   console.log(month);
      //   month = (month + 12) % 12; // 11 % 12 = 11
      //   this.showDate.month = month;
      // }
      // month += num;
      // if (month > 11) {
      //   month = 0;
      //   this.showDate.year ++
      // } else if (month < 0) {
      //   month = 11;
      //   this.showDate.year --
      // }
      // this.showDate.month = month;
      const showDate = new Date(year, month, day);
      showDate.setMonth(month + num);
      const { year: newYear, month: newMonth } = this.getYearMonthDay(showDate);
      this.showDate.year = newYear;
      this.showDate.month = newMonth;
    },
    isCur(date) {
      const chooseDate = new Date(this.dateFormat);
      const { year: chooseYear, month: chooseMonth, day: chooseDay } = this.getYearMonthDay(chooseDate);
      const { year, month, day } = this.getYearMonthDay(date);
      const { month: showMonth } = this.showDate;
      const { year: curYear, month: curMonth, day: curDay } = this.getYearMonthDay(new Date());
      return {
        month: month === showMonth,
        day: year === curYear && month === curMonth && day === curDay,
        select: year === chooseYear && month === chooseMonth && day === chooseDay
      }
    },
    chooseDate(date) {
      this.$emit('choose-date', date);
      this.showPanel(false)
      this.showDate = this.getYearMonthDay(date);
      
    },
    showPanel(flag) {
      this.show = flag;
    }
  }
}
</script>

<style scoped>
@import "./assets/font/iconfont.css";

.date-picker {
  display: inline-block;
}

.picker-input {
  position: relative;
}

.picker-input input {
  height: 40px;
  line-height: 40px;
  padding: 0 30px;
  background-color: #fff;
  border: 1px solid #dcdfe6;
  border-radius: 4px;
  outline: none;
  cursor: pointer;
}

.picker-input .input-prefix {
  position: absolute;
  left: 5px;
  width: 25px;
  height: 100%;
  line-height: 40px;
  text-align: center;
  color: #c0c4cc;
}

.picker-panel {
  position: absolute;
  width: 322px;
  height: 329px;
  margin-top: 5px;
  border: 1px solid #e4e7ed;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);
  background-color: #fff;
}

.picker-panel .picker-arrow {
  position: absolute;
  top: -12px;
  left: 30px;
  width: 0;
  height: 0;
  border: 6px solid transparent;
  border-bottom-color: #ebeef5;
}

.picker-panel .picker-arrow::after {
  position: absolute;
  left: -6px;
  top: 1px;
  content: '';
  display: block;
  width: 0;
  height: 0;
  border: 6px solid transparent;
  border-bottom-color: #fff;
  border-top-width: 0;
}

.picker-panel .picker-body {}

.picker-panel .picker-header {
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 15px;
  padding-bottom: 10px;
}
.picker-panel .picker-btn {
  margin-right: 5px;
  margin-left: 5px;
  font-size: 12px;
  color: #303133;
  cursor: pointer;
}

.picker-panel .picker-date {
  margin-left: 60px;
  margin-right: 60px;
  font-size: 14px;
  user-select: none;
}

.picker-panel .picker-content {
  padding: 0 10px 10px 10px;
  color: #606266;
  user-select: none;
}

.picker-panel .picker-weeks {
  display: flex;
  justify-content: space-around;
  height: 40px;
  line-height: 40px;
  border-bottom: 1px solid #ebeef5;
}

.picker-panel .picker-days {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.picker-panel .picker-days div {
  width: 30px;
  height: 30px;
  line-height: 30px;
  text-align: center;
  margin: 4px 6px;
  font-size: 12px;
  cursor: pointer;
}

.picker-panel .picker-days div:hover {
  color: #409eff;
}

.picker-panel .picker-days div.is-today {
  color: #409eff;
  font-weight: 700;
}

.picker-panel .picker-days div.is-select {
  border-radius: 50%;
  background-color: #409eff;
  color: #fff;
}


.picker-panel .picker-days div.other-month {
  color: #c0c4cc;
}
</style>
<template>
  <div class="box">
    <div class="top">
      <div>
        <span>{{year}}-{{month}}-{{date}}</span>
        <span
          class="today"
          @click="clickToday"
        >今天</span>
      </div>

    </div>
    <div
      class="calendar_box"
      @touchstart="touchStart($event)"
      @touchmove="touchMove($event,currentYear,currentMonth)"
      @touchend="touchEnd($event)"
    >
      <ul class="week">
        <li
          v-for="(item,index) in week"
          :key="index"
        >{{item}}</li>
      </ul>
      <ul
        class="dateofweek"
        v-if="flex"
      >
        <li
          v-for="(date,index) in week_dates"
          :key="index"
          :class="[{chooseDayClass:currentStr==date.dateformat},{todayBorder:todayDate==date.dateformat}]"
          @click="changeCurrentDate(date.dateStr,date.dateformat)"
        >{{date.date}}</li>
      </ul>
      <ul
        class="dateofmonth"
        v-else
      >
        <li
          v-for="(date,index) in month_dates"
          :key="index"
          :class="[{chooseDayClass:currentStr==date.dateformat},{todayBorder:todayDate==date.dateformat}]"
          @click="changeCurrentDate(date.dateStr,date.dateformat)"
        >{{date.date}}</li>
      </ul>
      <div
        class="arrow_box"
        :class="{arrow_box2:!flex}"
      > </div>
    </div>
    <div class="calendar_text">
      <ul>
        <li>已设置提醒</li>
        <li>创建时间</li>
        <li>最后发言时间</li>
      </ul>
    </div>
    <div style="background: rgb(242, 242, 242);overflow: hidden;padding: 5px 0 60px 0;">
      
      <ul class="list">
        <li
          v-for="(item,index) in listData"
          :key="index"
        >
          <div class="title">{{item.title}} <span class="fr clearfix">{{item.time}} </span></div>
          <div class="middle_text">{{item.middle_text}} </div>
          <div class="my_act">{{item.my_act}} <span class="fr clearfix">...</span> </div>
        </li>
      </ul>
    </div>
    <div class="tab">
      <div>
        <span class="tab-work"></span><br>
        工作
      </div>
      <div> <span class="tab-act"></span><br> 角色</div>
      <div> <span class="tab-thing"></span><br> 事项圈</div>
      <div> <span class="tab-colleage"></span><br> 同事</div>
      <div style="color:#2a579a;"> <span class="tab-calendar"></span><br> 日历</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      week: ["日", "一", "二", "三", "四", "五", "六"],
     
      year:'',
      month:'',
      date: '',

      currentYear:2019,
      currentMonth:1,
      currentDay:1,
      currentWeek:1,
      week_dates: [],  //本周的日期
      month_dates: [],  //本月的日期
      
      todayIndex: 0,         //当天日期的索引
      currentStr: false,      //当前选中日期的时间字符串
           

      flex: true,          //用来判断显示周还是月
      startY: 0,
      startX: 0,
      top: 0,
      leftDiff: 0,
      touching: false,    //当前是否属于下拉滑动行为
      state: 0,
      listData: [
        {
          id: 0,
          title: '227市场',
          middle_text: '定价依据客户判定',
          my_act: '我的角色',
          time: '19:23',
        },
        {
          id: 0,
          title: '227市场',
          middle_text: '定价依据客户判定',
          my_act: '我的角色',
          time: '19:23',
        },
        {
          id: 0,
          title: '227市场',
          middle_text: '定价依据客户判定',
          my_act: '我的角色',
          time: '19:23',
        },
        {
          id: 0,
          title: '227市场',
          middle_text: '定价依据客户判定',
          my_act: '我的角色',
          time: '19:23',
        },
      ]
    };
  },
  created() {
    this.getWeekDays(null);
    
  },
  computed: {
    defaultIndex: function () {
      let d = new Date()
      if (this.flex) {
        return d.getDay()
      } else {
        return d.getDate()
      }
    },
    todayDate:function(){
      let d=new Date()
      return this.formatDate(d.getFullYear(),d.getMonth(),d.getDate())
    }
  },

  methods: {
    /** 页面初始化时，有关日期的初始化  周显示 */
    getWeekDays(cur){
      let date
      if(cur){
        date=new Date(cur)
      }
      else{
        
        this.currentStr=this.todayDate    //初始化选中样式
        var now=new Date()

        this.year=now.getFullYear()  //初始化top的显示日期
        this.month=now.getMonth()+1
        this.date=now.getDate()

        date=new Date(this.formatDate(now.getFullYear(),now.getMonth()+1,now.getDate()))
      }

      this.currentYear=date.getFullYear()
      this.currentMonth=date.getMonth()+1
      this.currentDay=date.getDate()

      this.currentWeek=date.getDay()

      var str=this.formatDate(this.currentYear,this.currentMonth,this.currentDay)
      
      //存入本周的日期  将日期存为一个对象
      for(var i=this.currentWeek;i>=0;i--){
        let d=new Date(str)
        d.setDate(d.getDate()-i)
        let obj={}
        obj.dateStr=d
        obj.date=d.getDate()
        obj.dateformat=this.formatDate(d.getFullYear(),d.getMonth(),d.getDate())
        this.week_dates.push(obj)
      }

      for(var i=1;i<7- this.currentWeek;i++){
         let d=new Date(str)
        d.setDate(d.getDate()+i)
        let obj={}
        obj.dateStr=d
        obj.date=d.getDate()
        obj.dateformat=this.formatDate(d.getFullYear(),d.getMonth(),d.getDate())
        this.week_dates.push(obj)
      }
    },

    /** 上一周 */
    preWeek(year,month,day){
      this.week_dates=[]
      var d=new Date(this.formatDate(year,month,day))
      d.setDate(d.getDate()-7)
      this.getWeekDays(this.formatDate(d.getFullYear(),d.getMonth()+1,d.getDate()))

    },

    /** 下一周 */
    nextWeek(year,month,day){
    this.week_dates=[]
      var d=new Date(this.formatDate(year,month,day))
      d.setDate(d.getDate()+7)
      this.getWeekDays(this.formatDate(d.getFullYear(),d.getMonth()+1,d.getDate()))

    },

    /** 获取每个月的日期  月显示*/
    getMonthDays(cur){
        let date
        if(cur){
          date=new Date(cur)
        }else{
              // this.currentStr=this.todayDate
          let now=new Date()
          let d=new Date(this.formatDate(now.getFullYear(),now.getMonth(),1))
          d.setDate(35)
          date=new Date(this.formatDate(d.getFullYear(),d.getMonth()+1,1))  //本月的1号
        }

        this.currentDay=date.getDate()
        this.currentMonth=date.getMonth()+1
        this.currentYear=date.getFullYear()

        this.currentWeek=date.getDay()  //一号是礼拜几

        var str=new Date(this.formatDate(this.currentYear,this.currentMonth,this.currentDay)) //本月的1号

        //本周的日期
        for(let i=this.currentWeek;i>=0;i--){
          let e=new Date(str)
          let obj={}
          e.setDate(e.getDate()-i)
          obj.dateStr=e
          obj.dateformat=this.formatDate(e.getFullYear(),e.getMonth(),e.getDate())
          if(i==0){
            obj.date=e.getDate()
          }else{
                obj.date=''
          }
      
          this.month_dates.push(obj)
        }

        let str2=new Date(str)
        let f= str2.setMonth(str2.getMonth()+1)
        let f2=new Date(f)
        let f3=new Date(f2.setDate(0))
        

        //本月其他周的日期
        for(let i=1;i<f3.getDate();i++){
          let e=new Date(str)
          let obj={}
          e.setDate(e.getDate()+i)
           obj.dateStr=e
          obj.date=e.getDate()
           obj.dateformat=this.formatDate(e.getFullYear(),e.getMonth(),e.getDate())
          this.month_dates.push(obj)
        }

        
    },

    /** 获取上一个月 */
    pickPre(year,month){
      this.month_dates=[]
      var d=new Date(this.formatDate(year,month,1))
      d.setDate(0)
      this.getMonthDays(this.formatDate(d.getFullYear(),d.getMonth()+1,1))
    },

    /** 获取下一个月 */
    pickNext(year,month){
      this.month_dates=[]
       var d=new Date(this.formatDate(year,month,1))
       d.setDate(35)
       this.getMonthDays(this.formatDate(d.getFullYear(),d.getMonth()+1,1))
    },

    /** 格式化日期 */
    formatDate(year,month,day){
        let y=year
        let m=month
        let d=day
        if(m<10)m="0"+m;
        if(d<10) d='0'+d

        return y+'-'+m+'-'+d

    },

   

    /** 点击日期时样式的改变 */
    changeCurrentDate(dateStr,date) {
        this.currentStr=date

        this.year=dateStr.getFullYear()
        this.month=dateStr.getMonth()+1
        this.date=dateStr.getDate()
    },


    /** 点击今天，日期显示 */
    clickToday() {
      this.currentStr = this.todayDate
      if(this.flex){
        this.week_dates=[]
        this.getWeekDays(null)
        }
      else {
        this.month_dates=[]
        this.getMonthDays(null)
        }
    },

    /** 滑动开始 */
    touchStart(e) {
      this.startY = e.targetTouches[0].pageY
      this.startX = e.targetTouches[0].pageX
      this.touching = true

    },
    /** 滑动 */
    touchMove(e) {
      if (!this.touching) return

      //获取移动的距离
      let diff = e.targetTouches[0].pageY - this.startY
      let diff_row = e.targetTouches[0].pageX - this.startX
      this.top = Math.floor(diff * 0.25)
      this.leftDiff = Math.floor(diff_row * 0.25)

    },
    /** 滑动结束 */
    touchEnd(e) {
      this.touching = false

      if (this.top > 20) {
        this.flex = false
        this.getMonthDays(null)
        this.top=0
      } else if (this.top <= -10) {
        this.flex = true
        this.top = 0
      }
      if ((this.leftDiff > 15) && this.flex) {

       this.preWeek(this.currentYear,this.currentMonth,this.currentDay)
        this.leftDiff=0
      }
      if ((this.leftDiff <-15) && this.flex) {
       this.nextWeek(this.currentYear,this.currentMonth,this.currentDay)
        this.leftDiff=0
      }
      
      if (this.leftDiff > 15  && !this.flex) {
        this.pickPre(this.currentYear,this.currentMonth)
        this.leftDiff=0
      }
       if (this.leftDiff <-15  && !this.flex) {
        this.pickNext(this.currentYear,this.currentMonth)
        this.leftDiff=0
      }

    },

  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.top {
  height: 75px;
  background: #333;
  color: #fff;
  text-align: center;
  line-height: 100px;
}
.today {
  position: relative;
  top: 0;
  left: 100px;
}
.calendar_box {
  padding: 10px 20px;
  font-size: 14px;
}
.week,
.dateofweek,
.dateofmonth {
  display: flex;
}
.week li {
  flex: 1;
  color: #bcbcbc;
  text-align: center;
}
.dateofweek li {
  height: 40px;
  width: 40px;
  font-size: 18px;
  flex: 1;
  text-align: center;
  line-height: 40px;
  margin-right: 5px;
}
.dateofmonth {
  flex-wrap: wrap;
  padding-left: 5px;
}
.dateofmonth li {
  height: 40px;
  width: 40px;
  font-size: 18px;
  text-align: center;
  line-height: 40px;
  margin-right: 6px;
}
.chooseDayClass {
  border-radius: 50%;
  background: #2a579a;
  color: #fff;
}
.todayBorder {
  border-radius: 50%;
  border: 1px solid #2a579a;
}
.arrow_box {
  height: 18px;
  background: url("../assets/2.png") center center no-repeat;
}
.arrow_box2 {
  background: url("../assets/2.png") center center no-repeat;
  transform: rotate(180deg);
}
.calendar_text {
  box-shadow: 0px 0px 5px#bcbcbc;
}
.calendar_text ul {
  display: flex;
  font-size: 14px;
}
.calendar_text li {
  flex: 1;
  text-align: center;
  line-height: 40px;
}
.list {
  background: #fff;
}

.list li {
  height: 70px;
  border-bottom: 1px solid#bcbcbc;
  padding: 5px 15px;
  font-size: 14px;
}
.title {
  font-weight: bold;
  font-size: 18px;
  color: black;
}
.title span {
  font-size: 14px;
  color: #bcbcbc;
  font-weight: normal;
}
.middle_text {
  color: #bcbcbc;
}
.my_act {
  color: #53bde1;
}
.my_act span {
  font-weight: bold;
  font-size: 16px;
  color: #bcbcbc;
}
.tab {
  background: #fff;
  font-size: 14px;
  height: 55px;
  position: fixed;
  width: 100%;
  left: 0;
  bottom: 0;
  border-top: 1px solid #dcdcdc;
  box-shadow: #dcdcdc 10px 10px 0 0;
  display: flex;
}
.tab div {
  flex: 1;
  text-align: center;
  color: #bcbcbc;
}
.tab div span {
  display: inline-block;
  width: 30px;
  height: 27px;
}

.tab-work {
  background: url("../assets/1_0000_图层-1.png") center center no-repeat;
}
.tab-act {
  background: url("../assets/1_0002_图层-1.png") center center no-repeat;
}
.tab-thing {
  background: url("../assets/1_0003_图层-2.png") center center no-repeat;
}
.tab-colleage {
  background: url("../assets/1_0004_图层-3.png") center center no-repeat;
}
.tab-calendar {
  background: url("../assets/1_0001_图层-2.png") center center no-repeat;
}
</style>

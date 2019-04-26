<template>
  <section class="pickTimePage">
    <mt-popup position="bottom" class="pickTimeWrap" v-model="pickTime.model">
      <div class="pickTimeMain">
        <div class="pickHeader">
          <div class="item title" v-show="pickTime.type=='date'">请选择时间</div>
          <div class="item" v-show="pickTime.type!='date'" :class="{checked:timeType==='s'}" @click="_setTimeType('s')">
            <span class="text">开始时间</span>
          </div>
          <div class="item" v-show="pickTime.type!='date'" :class="{checked:timeType==='e'}" @click="_setTimeType('e')">
            <span class="text">结束时间</span>
          </div>
        </div>
        <div class="selectTime">
          <span @click="_prev" class="controlPrev"></span>
          <span class="text">
            {{selectDate.y}}-{{selectDate.m}}
          </span>
          <span @click="_next" class="controlNext"></span>
        </div>
        <div class="dataList" v-show="selectType==='d'">
          <div class="weekBox">
            <div class="item" v-for="(item,i) in headerName" :key="'headerName'+i">
              <span class="number">{{item}}</span>
            </div>
          </div>
          <div class="item prevDate" v-for="(item,i) in prevDate" :key="'prevDate'+i" @click="_prev(prevDateLast - (prevDate-item))"
            :class="{
              startTimeCheck: dataGap.start_time == _prevMonth+(prevDateLast - (prevDate-item)<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item)),
              endTimeCheck: dataGap.end_time == _prevMonth+(prevDateLast - (prevDate-item)<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item)),
              checked: dataGap.selectDate == _prevMonth+(prevDateLast - (prevDate-item)<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item)),
              TimeGaps:!(!dataGap.end_time)&&!(!dataGap.start_time)&&(dataGap.start_time.replace(/\//g,'')<prevMonth+(prevDateLast - (prevDate-item)<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item)))&&(prevMonth+(Number(prevDateLast - (prevDate-item))<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item))<dataGap.end_time.replace(/\//g,''))
            }"
            :datas="dataGap.start_time.replace(/\//g,'')+'------'+prevMonth+(prevDateLast - (prevDate-item)<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item))+'----------'+dataGap.end_time.replace(/\//g,'')+'------'+prevMonth+(Number(prevDateLast - (prevDate-item))<10?'0'+Number(prevDateLast - (prevDate-item)):prevDateLast - (prevDate-item))+'-----'+dataGap.end_time.replace(/\//g,'')"
          >
            <span class="number">{{prevDateLast - (prevDate-item)}}</span>
          </div>
          <div class="item" :class="{
            startTimeCheck: dataGap.start_time == selectDate.y+'/'+(selectDate.m<10?'0'+Number(selectDate.m):selectDate.m)+'/'+(item<10?'0'+Number(item):item),
            endTimeCheck: dataGap.end_time == selectDate.y+'/'+(selectDate.m<10?'0'+Number(selectDate.m):selectDate.m)+'/'+(item<10?'0'+Number(item):item),
            checked: dataGap.selectDate == selectDate.y+'/'+(selectDate.m<10?'0'+Number(selectDate.m):selectDate.m)+'/'+(item<10?'0'+Number(item):item),
            TimeGaps:!(!dataGap.end_time)&&!(!dataGap.start_time)&&dataGap.start_time.replace(/\//g,'')<thisMonth+(item<10?'0'+Number(item):item)&&thisMonth+(item<10?'0'+Number(item):item)<dataGap.end_time.replace(/\//g,'')}"
            v-for="(item,i) in monDate" :key="'monDate'+i" @click="_checkDate(item)">
            <span class="number">{{item}}</span>
          </div>
          <div class="item nextDate" v-for="(item,i) in nextMonthDate" :key="'nextMonth'+i"  @click="_next(item)"
            :class="{
              startTimeCheck: dataGap.start_time == _nextMonth+(item<10?'0'+Number(item):item),
              endTimeCheck: dataGap.end_time == _nextMonth+(item<10?'0'+Number(item):item),
              checked: dataGap.selectDate == _nextMonth+(item<10?'0'+Number(item):item),
              TimeGaps:!(!dataGap.end_time)&&!(!dataGap.start_time)&&dataGap.start_time.replace(/\//g,'')<nextMonth+(item<10?'0'+Number(item):item)&&nextMonth+(item<10?'0'+Number(item):item)<dataGap.end_time.replace(/\//g,'')
            }"
          >
            <span class="number">{{item}}</span>
          </div>
        </div>
        <div class="dataList monthList" v-show="selectType==='m'">
          <div class="item" v-for='(item,i) in 12' :key="'monthList'+i"><span class="number">{{item}}</span></div>
        </div>
        <div class="dataList yearList" v-show="selectType==='y'">
          <div class="item" v-for='(item,i) in 30' :key="'yearList'+i"><span class="number">{{item}}</span></div>
        </div>
      </div>
      <div class="pickTimeControl">
        <span class="reset" @click="pickTime.model = false">取消</span>
        <span class="submit" @click="_subMit">确认</span>
      </div>
    </mt-popup>  
  </section>  
</template>

<script>
export default {
  props:['pickTime'],
  computed:{
    nextMonthDate(){
      return 42 - this.monDate - this.prevDate
    },
    thisMonth(){
      return this.selectDate.y+''+(this.selectDate.m<10?'0'+Number(this.selectDate.m):this.selectDate.m)
    },
    nextMonth(){
      if(this.selectDate.m==12){
        return this.selectDate.y+1+'01'
      }else{
        return this.selectDate.y+(this.selectDate.m+1<10?'0'+Number(this.selectDate.m+1):this.selectDate.m+1)
      }
    },
    prevMonth(){
      if(this.selectDate.m==1){
        return this.selectDate.y-1+'12'
      }else{
        return this.selectDate.y+(this.selectDate.m-1<10?'0'+Number(this.selectDate.m-1):this.selectDate.m-1)
      }
    },
    _nextMonth(){
      if(this.selectDate.m==12){
        return this.selectDate.y+1+'/01/'
      }else{
        return this.selectDate.y+'/'+(this.selectDate.m+1<10?'0'+Number(this.selectDate.m+1):this.selectDate.m+1)+'/'
      }
    },
    _prevMonth(){
      if(this.selectDate.m==1){
        return this.selectDate.y-1+'/12/'
      }else{
        return this.selectDate.y+'/'+(this.selectDate.m-1<10?'0'+Number(this.selectDate.m-1):this.selectDate.m-1)+'/'
      }
    },

  },
  data(){
    return {
      deleteMsg:{model:false,msg:"请选择",type:'alert'},
      selectType:'d',
      timeType:'s',
      prevDate:0,
      prevDateLast:0,
      monDate:0,
      headerName:['日','一','二','三','四','五','六'],
      dataGap:{
        start_time:"",
        end_time:"",
        selectDate:""
      },
      selectDate:{
        y:"",
        m:"",
      }
    }
  },
  mounted(){
    this.selectDate.y=new Date().getFullYear();
    this.selectDate.m=new Date().getMonth()+1;
    this._getDate()
  },
  methods:{
    _setTimeType(type){
      this.timeType = type
    },
    _prev(date){
      if(this.selectDate.m==1){
        this.selectDate.y--
        this.selectDate.m=12
      }else{
        this.selectDate.m--
      }
      this._checkDate(date)
      this._getDate()
    },
    _next(date){
      if(this.selectDate.m==12){
        this.selectDate.y++
        this.selectDate.m=1
      }else{
        this.selectDate.m++
      }
      this._checkDate(date)
      this._getDate()
    },
    _checkDate(date){
      if(typeof date != 'number') return
      if(this.pickTime.type=='date'){
        this.dataGap.selectDate = this.selectDate.y+'/'+(this.selectDate.m<10?'0'+Number(this.selectDate.m):this.selectDate.m)+'/'+(date<10?'0'+Number(date):date)
        return 
      }


      if(this.timeType=='s'){
        this.dataGap.start_time = this.selectDate.y+'/'+(this.selectDate.m<10?'0'+Number(this.selectDate.m):this.selectDate.m)+'/'+(date<10?'0'+Number(date):date)
        this.timeType = "e"
      }else if(this.timeType=='e'){
        this.dataGap.end_time = this.selectDate.y+'/'+(this.selectDate.m<10?'0'+Number(this.selectDate.m):this.selectDate.m)+'/'+(date<10?'0'+Number(date):date)
      }
      if(this.dataGap.start_time.replace(/\//g,'')>this.selectDate.y+''+(this.selectDate.m<10?'0'+Number(this.selectDate.m):this.selectDate.m)+''+(date<10?'0'+Number(date):date)){
        this.dataGap.start_time = this.selectDate.y+'/'+(this.selectDate.m<10?'0'+Number(this.selectDate.m):this.selectDate.m)+'/'+(date<10?'0'+Number(date):date)
        this.dataGap.end_time = ''
        this.timeType = "e"
      }
    },
    _getDate(){
      // 上月最后一天
      let prveDate = new Date(this.selectDate.y+'/'+this.selectDate.m+'/1 00:00:00')-1;
      this.prevDate = new Date(prveDate).getDay()+1
      this.prevDateLast = new Date(prveDate).getDate()
      // 本月天数
      switch (this.selectDate.m){
        case 12:
          let monthTime = new Date(this.selectDate.y+1+'/1/1 00:00:00')-1;
          this.monDate = new Date(monthTime).getDate()
        break;
        default:
          let monthTimes = new Date(this.selectDate.y+'/'+Number(this.selectDate.m+1)+'/1 00:00:00')-1;
          this.monDate = new Date(monthTimes).getDate()
      }
    },
    _subMit(){
      if(this.pickTime.type == 'date'){
        if(!this.dataGap.selectDate){
          this.deleteMsg.model=true;
          this.deleteMsg.msg = '请选择时间'
          return      
        }
        this.$emit("subMit",this.dataGap.selectDate)
        this.pickTime.model = false
      }else{
        if(!this.dataGap.start_time){
          this.deleteMsg.model = true
          this.deleteMsg.msg = '请选择开始时间'
          return
        }
        if(!this.dataGap.end_time){
          this.deleteMsg.model = true
          this.deleteMsg.msg = '请选择结束时间'
          return
        }
        this.$emit("subMit",{start_time:this.dataGap.start_time.replace(/\//g,'-'),end_time:this.dataGap.end_time.replace(/\//g,'-')})
        this.pickTime.model = false
      }
    }
  },
}
</script>

<style lang="stylus">
  .pickTimePage
    .mt-popup
      z-index 9
    .pickTimeWrap
      height 22.5rem
      overflow auto
      width 100%
      display flex
      flex-direction column
      .pickTimeMain
        flex 1
        .pickHeader
          display flex
          justify-content space-around
          height 2.45rem
          border-bottom 1px solid $C7
          .item
            text-align center
            display flex
            flex-direction column
            justify-content center
            font-size $S3
            border-bottom 2px solid transparent
            color $C11
            margin-bottom -1px
            &.checked
              border-color $C1
              color $C9
            &.title
              color $C9
        .selectTime
          display flex
          justify-content center
          height 1.7rem
          font-size .9rem
          color $C10
          border-bottom 1px solid $C7
          span 
            display flex
            flex-direction column
            justify-content center
          .controlPrev
            width .75rem
            margin-right .5rem
            background url(../../common/images/control_prev.png) no-repeat center center/auto .5rem
          .controlNext
            margin-left .5rem
            width .75rem
            background url(../../common/images/control_next.png) no-repeat center center/auto .5rem
        .dataList
          font-size $S3
          P(.2rem 0)
          display flex
          flex-wrap wrap 
          .weekBox
            border-bottom 1px solid $C7
            display flex
            width 100%
            M(0 0 .2rem 0)
          .item
            height 1.6rem
            text-align center
            display flex
            flex-direction column
            justify-content center
            margin-bottom .2rem
            width calc(100% / 7)
            &.prevDate
            &.nextDate
              color $C12
            .number
              width 1.6rem
              height 1.6rem
              line-height 1.6rem
              display block
              M(0 auto)
              borderRadius(1.6rem)

            &.checked
              .number
                background $C1
            &.endTimeCheck
            &.startTimeCheck
              border-color $C1
              color $C5
              position relative
              &:after
                content ''
                display block 
                position absolute 
                height 100%
                top 0
                left 0
                background #bee2fe
                z-index -1
              .number
                background $C1
              &.endTimeCheck
                &:after
                  width 50%
                  left 0
              &.startTimeCheck
                &:after
                  width 50%
                  right 0
                  left auto
            &.endTimeCheck.startTimeCheck
              &:after
                display none

            &.TimeGaps
              position relative
              &:after
                content ''
                display block 
                position absolute 
                top 0
                left 0
                height 100%
                width 100%
                background #bee2fe
                z-index -1
      .pickTimeControl
        height 2.2rem
        display flex
        text-align center
        border-top 1px solid $C7
        color $C9
        font-size $S3
        .reset
        .submit
          flex 1
          display flex
          flex-direction column
          justify-content center

        .submit
          background $C1
          color $C5
</style>


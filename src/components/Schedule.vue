<template>
<div id="body">
    <header>
        <div class="prev" @click="header.prev.handleClick">
            <span class="fa fa-angle-left"></span>
            <span>{{header.prev.text}}</span>
        </div>
        <div class="title" @click="header.handleClick">{{header.title}}</div>
        <div class="next" @click="header.next.handleClick">
            <span>{{header.next.text}}</span>
            <span class="fa fa-angle-right"></span>
        </div>
    </header>
    <main>
        <ul class="schedule-list">
            <li v-for="(schedule, index) in schedules" v-bind:class="colorList[index%colorList.length]">
                <div class="schedule-date"><div>{{schedule.dateFormat}}</div></div>
                <ul v-if="schedule.list" class="schedule-content">
                    <li v-for="item in schedule.list">
                        <div class="content-title">
                            <div>{{item.title}}</div>
                            <div class="schedule-time" v-if="item.timeFormat">
                                <div v-bind:class="{long:item.timeFormat.length>5}">{{item.timeFormat}}</div>
                            </div>
                        </div>
                        <ul v-if="item.list" class="content-list">
                            <li v-for="thing in item.list">{{thing}}</li>
                        </ul>
                    </li>
                </ul>
                <div v-else class="schedule-empty">无日程</div>
            </li>
        </ul>
    </main>
    <footer>
        <div class="left-menu">
            <!-- <button class="fa fa-chevron-circle-left" aria-hidden="true"></button> -->
        </div>
        <div class="right-menu">
            <button class="fa fa-refresh" aria-hidden="true" @click="refreshSchdule"></button>
            <!-- <button class="fa fa-chevron-circle-right" aria-hidden="true"></button> -->
        </div>
    </footer>
    <transition name="fade">
        <div id="calendar" v-show="calendar.show" v-on:click.stop="calendar.handleClick">
            <div class="calendar-wrapper" v-on:click.stop="calendar.handleChoose">
                <div class="calendar-title">
                    <div>日</div>
                    <div>一</div>
                    <div>二</div>
                    <div>三</div>
                    <div>四</div>
                    <div>五</div>
                    <div>六</div>
                </div>
                <ul class="calendar-content">
                    <li v-for="row in calendar.data">
                        <div v-for="cell in row" :class="{'active':cell.active,'tody':cell.tody,'disabled':cell.disabled}">
                            <span v-bind:title="cell.date.Format('yyyy-MM-dd')">{{cell.text}}</span>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </transition>
</div>
</template>

<script>
export default {
  name: 'schedule',
  data () {
    var now = new Date(new Date().Format("yyyy-MM-dd")),
        current = new Date(new Date().Format("yyyy-MM-dd"));
    var header = {
        title:now.Format("MM.dd"),
        handleClick:function(){
            calendar.show = "show";
        },
        setDate:function(date){
            current = date || new Date(new Date().Format("yyyy-MM-dd"))

            header.prev.text = now-current==0?"昨天":new Date((current.getTime() - 24 * 60 * 60 * 1000)).Format("MM.dd");
            header.next.text = now-current==0?"明天":new Date((current.getTime() + 24 * 60 * 60 * 1000)).Format("MM.dd");
            header.title = current.Format("MM.dd");
            setScheduleDate(current)
            calendar.render(current);
        },
        prev:{
            text:"昨天",
            disable:false,
            handleClick:function(){
                current = new Date(current.getTime() - 24 * 60 * 60 * 1000)

                header.prev.text = now-current==0?"昨天":new Date((current.getTime() - 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.next.text = now-current==0?"明天":new Date((current.getTime() + 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.title = current.Format("MM.dd");
                setScheduleDate(current)
                calendar.render(current);
            }
        },
        next:{
            text:"明天",
            disable:false,
            handleClick:function(){
                current = new Date(current.getTime() + 24 * 60 * 60 * 1000)

                header.prev.text = now-current==0?"昨天":new Date((current.getTime() - 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.next.text = now-current==0?"明天":new Date((current.getTime() + 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.title = current.Format("MM.dd");
                setScheduleDate(current)
                calendar.render(current);
            }
        }
    }
    var schedules = (function(datas){
        var list = [];
        if(datas && datas.length >0){
            for(var i=0;i<datas.length;i++){
                var schedule = datas[i];
                if(schedule.date && !schedule.dateFormat){
                    switch(typeof schedule.date){
                        case "object":
                            if(schedule.date.startDate && schedule.date.endDate){
                                schedule.date.startDate = new Date(schedule.date.startDate)
                                schedule.date.endDate = new Date(schedule.date.endDate)
                                schedule.dateFormat = schedule.date.startDate.Format("MM.dd") + " 周" + "日一二三四五六".split("")[schedule.date.startDate.getDay()];
                                schedule.dateFormat += " - " + schedule.date.endDate.Format("MM.dd") + " 周" + "日一二三四五六".split("")[schedule.date.endDate.getDay()];
                            }
                            break;
                        case "string":
                        case "number":
                            var date = new Date(schedule.date);
                            schedule.date = {
                                startDate:date,
                                endDate:new Date(date.getTime() + (24 * 60 * 60 * 1000 -1)),
                            }
                            schedule.dateFormat = schedule.date.startDate.Format("MM.dd") + " 周" + "日一二三四五六".split("")[schedule.date.startDate.getDay()];
                            break;
                    }
                }
                if(schedule.list && schedule.list.length > 0){
                    var day = [];
                    for(var j=0;j<schedule.list.length;j++){
                        var item = schedule.list[j];
                        if(item.time){
                            switch(typeof item.time){
                                case "object":
                                    if(item.time.startTime && item.time.endTime){
                                        item.timeFormat = item.time.startTime + " " + item.time.endTime;
                                    }
                                    break;
                                case "string":
                                    item.timeFormat = item.time;
                                    break;
                            }
                        }


                        day.push(item);
                    }
                    schedule.list = day;
                }




                list.push(schedule);
            }
        }
        if(list[0]){
            var schedule = list[0];
            console.log(schedule.date.startDate.getDate())
            console.log(now)
            if(schedule.date && schedule.date.startDate && schedule.date.startDate - now > 0){
                list.unshift({
                    date:{
                        startDate:now,
                        endDate:new Date(schedule.date.startDate.getTime() - 24 * 60 * 60 * 1000)
                    },
                })
                if(schedule.date.startDate - list[0].date.startDate < 24 * 60 * 60 * 1000){
                    list[0].dateFormat = "今天"
                }else{
                    list[0].dateFormat = "今天 - " + new Date(schedule.date.startDate.getTime() - 24 * 60 * 60 * 1000).Format("MM.dd")
                }
            }
        }else{
            list = [
                {
                    date:{
                        startDate:now,
                        endDate:new Date(now.getTime() + 24 * 60 * 60 * 1000)
                    },
                    dateFormat:new Date().Format("MM.dd")
                }
            ]
        }
        return list;
    })(window.scheduleData || []);

    // console.log(schedules)
    var setScheduleDate = function(date){
        date = date || new Date();
        for(var i=0;i<schedules.length;i++){
            var schedule = schedules[i];
            if(date >= schedule.date.startDate && date <= schedule.date.endDate){
                console.log(schedule)
                var fx = document.querySelectorAll(".schedule-list .fixed");
                if(fx.length>0){
                    fx[0].className = "schedule-date";
                }
                var sc = document.querySelectorAll(".schedule-list>li")[i];
                if(i>0){
                    sc.getElementsByClassName("schedule-date")[0].className = "schedule-date fixed";
                }
                window.scrollTo(0,sc.offsetTop - 50)
            }
        }
    }
    var calendar = {
        show:"",
        handleClick:function(){
            calendar.show = "";
        },
        handleChoose:function(event){
            var date = "";
            if(event.target.tagName == "SPAN"){
                date = new Date(event.target.title);
                header.setDate(date);
                calendar.show = "";
            }
        },
        data:[],
        render:function(date){
            date = date || new Date();
            var firstDay = new Date(date.Format("yyyy-MM-01 00:00:00"));
            this.data = [[]];
            for(var i=0;i<=firstDay.getDay();i++){
                var data = {date:new Date(firstDay.getTime() - i*24*60*60*1000),active:false,tody:false,disabled:true}
                data.text = data.date.getDate();
                this.data[0][data.date.getDay()] = data;
            }
            for(;firstDay.getMonth() == date.getMonth();firstDay=new Date(firstDay.getTime() + 24*60*60*1000)){
                var data = {text:firstDay.getDate(),date:new Date(firstDay.getTime()),active:false,tody:false,disabled:false},
                    day = firstDay.getDate(),
                    week = firstDay.getDay();
                if(firstDay.Format("yyyy-MM-dd") == date.Format("yyyy-MM-dd"))
                    data.active = true;
                if(firstDay.Format("yyyy-MM-dd") == now.Format("yyyy-MM-dd"))
                    data.tody = true;
                if(day%7 <= week){
                    if(!this.data[parseInt(day/7)])
                        this.data[parseInt(day/7)] = [];
                    this.data[parseInt(day/7)][week] = data
                }else{
                    if(!this.data[parseInt(day/7+1)])
                        this.data[parseInt(day/7+1)] = [];
                    this.data[parseInt(day/7+1)][week] = data
                }
                console.log(this.data)
            }
            if(this.data[0].length<7) this.data.shift();
            if(firstDay.getDay()>0)
                for(var i=firstDay.getDay();i<=6;i++){
                    var data = {date:new Date(firstDay.getTime() + (i-firstDay.getDay())*24*60*60*1000),active:false,tody:false,disabled:true}
                    data.text = data.date.getDate();
                    this.data[this.data.length-1][data.date.getDay()] = data;
                }
        }
    }
    return {
        header:header,
        colorList:["default","red","orange","yellow","green","blue","purple"],
        schedules:schedules,
        refreshSchdule:function(){setScheduleDate()},
        calendar:calendar
    }
  },
  created:function(){
      window.onscroll = function(){
        var st = document.documentElement.scrollTop || document.body.scrollTop;
        // console.log(st)
        var scList = document.querySelectorAll(".schedule-list>li")
        for(var i=0;i<scList.length;i++){
            var sc = scList[i];
            var sd = sc.getElementsByClassName("schedule-date")[0];
            sd.className = "schedule-date";
            var scTop = sc.offsetTop - 51,
                scHeight = sc.offsetHeight;
            // console.log(i + " : " + sc.offsetTop)
            if(st > scTop && st < sc.offsetTop + scHeight){
                sd.className = "schedule-date fixed";
            }
        }
      }
    //   this.refreshSchdule();
      this.calendar.render();
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#body{

}
main{
    padding: 51px 0;
}
footer,header{
    width: 100%;
    height: 50px;
    line-height: 50px;
    position: fixed;
    z-index: 2;
    display: box;
    display: -webkit-box;
    display: -moz-box;
    display: -ms-box;
    display: flex;
    display: -webkit-flex;
    display: -moz-flex;
    display: -ms-flex;
    box-orient:horizontal;
    -webkit-box-orient:horizontal;
    -moz-box-orient:horizontal;
    -ms-box-orient:horizontal;
    flex-direction:row;
    -webkit-flex-direction:row;
}
header{
    top: 0;
    left: 0;
    background-color: #fff;
    border-bottom: 1px solid #35495e;
    color:#35495e;
}
header .prev,header .next{
    padding: 0 10px;
}
header .title::after{
    content: "●";
    color: #41b883;
}
footer{
    background-color: #41b883;
    color: #fff;
    bottom: 0;
    left:0;
}
footer>div,header .title{
    box-flex:1;-webkit-box-flex:1;-moz-box-flex:1;-ms-box-flex:1;
    flex:1;-webkit-flex:1;-moz-flex:1;-ms-flex:1;
}
footer>div.left-menu{
    text-align: left;
}
footer>div.right-menu{
    text-align: right;
}
footer button{
    height: 50px;
    width: 50px;
    color: #fff;
    outline: none;
    border: none;
    background-color: #41b883;
}
footer>div.left-menu button{
    border-right: 1px solid #fff;
}
footer>div.right-menu button{
    border-left: 1px solid #fff;
}
.schedule-empty{
    padding:10px 20px;

}
main .schedule-date{
    color: #35495e;
    background-color: #fff;
}
main .schedule-date.fixed{
    position: fixed;
    top: 50px;
    left: 0;
    width: 100%;
}
main .schedule-date div{
    padding-left: 10px;
    background-color: rgba(200, 200, 200, .2)
}
.schedule-list{
    text-align: left;
}
.schedule-list>li{
    background-color: rgba(200, 200, 200, .05)
}
.schedule-list>li:first-of-type{
    border-top: none;
}
.schedule-content{
    padding-left: 10px;
    
}

.content-list{
    padding-left: 10px;
}
.schedule-content .content-list>li{
    border-top: 1px dashed #ccc;
    padding: 10px 0 10px 10px;
    font-size: 14px;
}
.content-title{
    border-top: 1px solid #35495e;
    padding: 10px 0 10px 10px;
    height: 24px;
    list-style: 24px;
    display: box;
    display: -webkit-box;
    display: -moz-box;
    display: -ms-box;
    display: flex;
    display: -webkit-flex;
    display: -moz-flex;
    display: -ms-flex;
    box-orient:horizontal;
    -webkit-box-orient:horizontal;
    -moz-box-orient:horizontal;
    -ms-box-orient:horizontal;
    flex-direction:row;
    -webkit-flex-direction:row;
}
.content-title>div:first-of-type{
    box-flex:1;-webkit-box-flex:1;-moz-box-flex:1;-ms-box-flex:1;
    flex:1;-webkit-flex:1;-moz-flex:1;-ms-flex:1;
}
.content-title .schedule-time{
    position: relative;
    width: 40px;
    padding-right: 10px;
    font-size: 14px;
}
.content-title .schedule-time div{
    
}
.content-title .schedule-time div.long{
    position: absolute;
    top: -7px;
    left: 0;
    width: 40px;
}
.schedule-content>li:first-of-type .content-title{
    border-top: none;
}
.schedule-content .content-list>li:last-of-type{
    border-bottom: none;
}
.schedule-list>li.red .schedule-date div{
    background-color: rgba(232, 76,61, .2)
}
.schedule-list>li.red{
    background-color: rgba(232, 76,61, .05)
}
.schedule-list>li.yellow .schedule-date div{
    background-color: rgba(241, 196,15, .2)
}
.schedule-list>li.yellow{
    background-color: rgba(241, 196,15, .05)
}
.schedule-list>li.orange .schedule-date div{
    background-color: rgba(244, 156,20, .2)
}
.schedule-list>li.orange{
    background-color: rgba(244, 156,20, .05)
}
.schedule-list>li.blue .schedule-date div{
    background-color: rgba(84, 213,235, .2)
}
.schedule-list>li.blue{
    background-color: rgba(84, 213,235, .05)
}
.schedule-list>li.purple .schedule-date div{
    background-color: rgba(156, 89,184, .2)
}
.schedule-list>li.purple{
    background-color: rgba(156, 89,184, .05)
}
.schedule-list>li.green .schedule-date div{
    background-color: rgba(47, 204,113, .2)
}
.schedule-list>li.green{
    background-color: rgba(47, 204,113, .05)
}
#calendar{
    position: fixed;
    top: 51px;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, .6);
    z-index: 3;
    overflow: hidden;
}
#calendar.fade-enter-active, #calendar.fade-leave-active {
    transition: all .5s ease
}
#calendar.fade-enter /* .fade-leave-active in below version 2.1.8 */ {
    opacity: 0;
}

#calendar.fade-leave-to{
    opacity: 0;
}

.calendar-wrapper{
    background-color: #fff;
    position: relative;
    top: 0;
}
#calendar.fade-enter-active .calendar-wrapper{
    transition: all .5s ease
}
#calendar.fade-enter .calendar-wrapper{
    top: -200px;
}


.calendar-title{
    border-bottom: 1px solid #ddd;
}
.calendar-title,.calendar-content li{
    display: box;
    display: -webkit-box;
    display: -moz-box;
    display: -ms-box;
    display: flex;
    display: -webkit-flex;
    display: -moz-flex;
    display: -ms-flex;
    box-orient:horizontal;
    -webkit-box-orient:horizontal;
    -moz-box-orient:horizontal;
    -ms-box-orient:horizontal;
    flex-direction:row;
    -webkit-flex-direction:row;
}
.calendar-title>div,.calendar-content li>div{
    box-flex:1;-webkit-box-flex:1;-moz-box-flex:1;-ms-box-flex:1;
    flex:1;-webkit-flex:1;-moz-flex:1;-ms-flex:1;
    padding: 1% 0;
}
.calendar-content li>div span{
    display: inline-block;
    height: 30px;
    line-height: 30px;
    width: 30px;
    border-radius: 4px;
}
.calendar-content li>div.tody span{
    
    color: #41b883;
    border:1px solid #41b883;
    height: 29px;
    line-height: 29px;
    width: 29px;
}
.calendar-content li>div.active span{
    background-color: #41b883;
    color: #fff;
}
.calendar-content li>div.disabled{
    color: #aaa;
}
</style>

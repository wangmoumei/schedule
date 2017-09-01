<template>
<div id="body">
    <header>
        <div class="prev" @click="header.prev.hanldeClick">
            <span class="fa fa-angle-left"></span>
            <span>{{header.prev.text}}</span>
        </div>
        <div class="title">{{header.title}}</div>
        <div class="next" @click="header.next.hanldeClick">
            <span>{{header.next.text}}</span>
            <span class="fa fa-angle-right"></span>
        </div>
    </header>
    <main>
        <ul class="schedule-list">
            <li v-for="(schedule, index) in schedules" v-bind:class="colorList[index%colorList.length]">
                <div class="schedule-date">{{schedule.dateFormat}}</div>
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

        </div>
        <div class="right-menu">

        </div>
    </footer>
</div>
</template>

<script>
export default {
  name: 'schedule',
  data () {
    var now = new Date(),
        current = new Date();
    var header = {
        title:now.Format("MM.dd"),
        prev:{
            text:"昨天",
            disable:false,
            hanldeClick:function(){
                current = new Date(current.getTime() - 24 * 60 * 60 * 1000)

                header.prev.text = now-current==0?"昨天":new Date((current.getTime() - 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.next.text = now-current==0?"明天":new Date((current.getTime() + 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.title = current.Format("MM.dd");
            }
        },
        next:{
            text:"明天",
            disable:false,
            hanldeClick:function(){
                current = new Date(current.getTime() + 24 * 60 * 60 * 1000)

                header.prev.text = now-current==0?"昨天":new Date((current.getTime() - 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.next.text = now-current==0?"明天":new Date((current.getTime() + 24 * 60 * 60 * 1000)).Format("MM.dd");
                header.title = current.Format("MM.dd");
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
                                endDate:new Date(date.getTime() + 24 * 60 * 60 * 1000),
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
                        endDate:new Date(now.getTime() + 24 * 60 * 60 * 1000)
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

    console.log(schedules)
    
    return {
        header:header,
        colorList:["default","red","yellow","orange","blue","purple","green"],
        schedules:schedules
    }
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
    bottom: 0;
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
.schedule-empty{
    padding:10px 20px;

}
main .schedule-date{
    color: #35495e;
    padding-left: 10px;
    background-color: rgba(65, 184, 131, .2)
}
.schedule-list{
    text-align: left;
}
.schedule-list>li{
    background-color: rgba(65, 184, 131, .05)
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
.schedule-list>li.red .schedule-date{
    background-color: rgba(232, 76,61, .2)
}
.schedule-list>li.red{
    background-color: rgba(232, 76,61, .05)
}
.schedule-list>li.yellow .schedule-date{
    background-color: rgba(241, 196,15, .2)
}
.schedule-list>li.yellow{
    background-color: rgba(241, 196,15, .05)
}
.schedule-list>li.orange .schedule-date{
    background-color: rgba(244, 156,20, .2)
}
.schedule-list>li.orange{
    background-color: rgba(244, 156,20, .05)
}
.schedule-list>li.blue .schedule-date{
    background-color: rgba(84, 213,235, .2)
}
.schedule-list>li.blue{
    background-color: rgba(84, 213,235, .05)
}
.schedule-list>li.purple .schedule-date{
    background-color: rgba(156, 89,184, .2)
}
.schedule-list>li.purple{
    background-color: rgba(156, 89,184, .05)
}
.schedule-list>li.green .schedule-date{
    background-color: rgba(47, 204,113, .2)
}
.schedule-list>li.green{
    background-color: rgba(47, 204,113, .05)
}
</style>

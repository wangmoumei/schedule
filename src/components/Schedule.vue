<template>
<div id="body">
    <header>
        <div class="prev" v-on:click="header.prev.hanldeClick">{{header.prev.text}}</div>
        <div class="title">{{header.title}}</div>
        <div class="next" v-on:click="header.next.hanldeClick">明天</div>
    </header>
    <main>
        <ul class="schedule-list">
            <li>

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
  name: 'test',
  data () {
    var now = new Date(),
        current = now;
    var header = {
        title:now.Format("MM.dd"),
        prev:{
            text:"昨天",
            disable:false,
            hanldeClick:function(){
                current = new Date(current.setMilliseconds(current.getMilliseconds() - 24 * 60 * 60 * 1000))
                header.title = current.Format("MM.dd");
            }
        },
        next:{
            text:"明天",
            disable:false,
            hanldeClick:function(){
                current = new Date(current.setMilliseconds(current.getMilliseconds() + 24 * 60 * 60 * 1000))
                header.title = current.Format("MM.dd");
            }
        }
    }
    return {
        header:header
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#body{
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    display: box;
    display: -webkit-box;
    display: -moz-box;
    display: -ms-box;
    display: flex;
    display: -webkit-flex;
    display: -moz-flex;
    display: -ms-flex;
    box-orient:vertical;
    -webkit-box-orient:vertical;
    -moz-box-orient:vertical;
    -ms-box-orient:vertical;
    flex-direction:column;
    -webkit-flex-direction:column;
}
main{
    box-flex:1;-webkit-box-flex:1;-moz-box-flex:1;-ms-box-flex:1;
    flex:1;-webkit-flex:1;-moz-flex:1;-ms-flex:1;
}
footer,header{
    height: 50px;
    line-height: 50px;
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
ul.date-list{
    overflow: hidden;
    padding: 10px;
    text-align: center;
}
.date-list li{
    display: inline-block;
    border:1px solid #41b883;
    border-radius: 4px;
    padding: 4px 6px;
    margin: 6px 4px;
    color: #41b883;
    cursor: pointer;
}
.date-list li.active{
    color: #fff;
    background-color: #41b883;
}
</style>

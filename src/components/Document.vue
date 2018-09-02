<template>
<div class="document rolling-table">
  <table id="roll-table" @scroll="handleScroll">
    <tr class="header"><th></th><th v-for="(item, index) in headers" :key="index">{{item}}</th></tr>
    <tr v-for="(item, index) in files" :key="index"><td>{{index}}</td><td>{{item.name}}</td></tr>
  </table>
  <div class="top-left"></div>
  <table class="left-fixed" id="left-table">
    <tr><td></td></tr>
    <tr v-for="(tr, index) in files" :key="index">
      <td v-for="(item,index) in tr" :key="index">{{item.name}}</td>
    </tr>
  </table>
  <table class="top-fixed" id="top-table">
     <tr class="header"><th></th><th v-for="(item, index) in headers" :key="index">{{item}}</th></tr>
  </table>
</div>
</template>
<script>
const iScoll = require('iscroll/build/iscroll-probe')
export default {
  data () {
    return {
      headers:['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W',' X', 'Y', 'Z', 'AA', 'AB'],
      files:[[{name:'哈哈'},{name:'哈哈'},{name:'哈哈'},{name:'哈哈'},{name:'哈哈'},{name:'哈哈'},{name:'哈哈'},], 
      {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'},
      {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'}, {name:'哈哈'},
      ],
      showLeft: false,
      showTop: false,
    }
  },
  mounted() {
    this.scroll = new iScoll('.rolling-table', {
      preventDefault: false,  // 阻止浏览器滑动默认行为
      probeType: 3, //需要使用 iscroll-probe.js 才能生效 probeType ： 1 滚动不繁忙的时候触发 probeType ： 2 滚动时每隔一定时间触发 probeType ： 3   每滚动一像素触发一次
      mouseWheel: true, //是否监听鼠标滚轮事件。
      scrollX: true,  // 启动x轴滑动
      scrollY: true,  // 启动y轴滑动
      // momentum: false,
      lockDirection: false,
      snap: false, //自动分割容器，用于制作走马灯效果等。Options.snap:true// 根据容器尺寸自动分割
      //snapSpeed: 400,
      scrollbars: false, //是否显示默认滚动条
      freeScroll: false, //主要在上下左右滚动都生效时使用，可以向任意方向滚动。
      deceleration: 0.0001, //滚动动量减速越大越快，建议不大于 0.01,默认:0.0006
      disableMouse: true, //是否关闭鼠标事件探测。如知道运行在哪个平台，可以开启它来加速。
      disablePointer: true, //是否关闭指针事件探测。如知道运行在哪个平台，可以开启它来加速。
      disableTouch: false, //是否关闭触摸事件探测。如知道运行在哪个平台，可以开启它来加速。
      eventPassthrough: false, //使用 IScroll 的横轴滚动时，如想使用系统立轴滚动并在横轴上生效，请开启。
      bounce: false //是否启用弹力动画效果，关掉可以加速
    });
    this.rollTable = document.getElementById('roll-table');
    this.leftTable = document.getElementsByClassName('left-fixed');
    this.topTable = document.getElementsByClassName('top-fixed');
    // this.scroll.on('scroll',this.handleScroll);
  },
  methods: {
    handleScroll() {
      console.log(document.getElementById('roll-table').scrollLeft);
      //document.getElementById('left-table').style.transform = `translate(0, ${this.scroll.y}px)`;
      //document.getElementById('top-table').style.transform = `translate(${this.scroll.x}px, 0)`;
      document.getElementById('left-table').style.top = `${this.scroll.y}px`;
      document.getElementById('top-table').style.left = `${this.scroll.x}px`;   
    }
  }
}
</script>
<style lang="less">
.document {
  height: 100%;
  width: 100%;
  .top-left {
    position: absolute;
    top: 0px;
    left: 0px;
    width: 63px;
    height: 43px;
    z-index: 100;
    background-color: #ddd;
    border: 1px solid #eee;
  }
  table{
    tr.header{
    }
    th{
      min-width: 100px;
      height: 40px;
      background-color: #ddd;
      border: 1px solid #eee;
      text-align: center;
      color: #888;
    }
    th:first-child{
      min-width: 60px;
    }
    tr td:first-child{
      min-width: 60px;
      background-color: #ddd;
      border: 1px solid #eee;
      color: #888;
      text-align: center;
    }
    tr td{
      min-width: 100px;
      border: 1px solid #eee;
    }
  }
  table.left-fixed {
    tr:first-child{
      height: 43px;
    }
    position: absolute;
    top: 0;
    left: 0;
  }
  table.top-fixed {
    position: absolute;
    top: 0;
    left: 0;
  }
}
</style>


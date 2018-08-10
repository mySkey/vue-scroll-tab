<style scoped>
ul li{list-style: none}
.withAnimate {
  transition: all 300ms ease;
  -webkit-transform: translate3d(0, 0, 0);-moz-transform: translate3d(0, 0, 0);-ms-transform: translate3d(0, 0, 0);transform: translate3d(0, 0, 0);-webkit-backface-visibility: hidden;-moz-backface-visibility: hidden;-ms-backface-visibility: hidden;backface-visibility: hidden;-webkit-perspective: 1000;-moz-perspective: 1000;-ms-perspective: 1000;perspective: 1000;
}
.nav-container{overflow: hidden;position: relative;}

.nav-title{width:100%;display: flex;height: 40px;justify-content: space-around;align-items: center;position: relative;}


.content{width:100%;height:calc(100vh - 40px);position: relative;top:0;left: 0;}
.content-slider{height:100%;display:flex;position:absolute;top:0;left:0;}
.content-item{height:100%;background: pink;}
</style>

<template>
  <div class="nav-container">
    <ul class="nav-title">
      <li class="title-item"  @click="select(k)" v-for="(v,k) in titleArr" :key="k">{{v}}</li>
      <div :class="{'withAnimate': !state.tStart}" :style="`width:${state.lineWidth}px;left:${state.offset/count}px;height:2px;background:skyblue;position:absolute;bottom:0;`"></div>
    </ul> 
    
    <div class="content" :style="`height:${height}px;`">
      <ul :class="{'withAnimate': !state.tStart}" class="content-slider" 
        :style="`width:${state.windowWidth*count}px;left:${-state.offset}px`" 
        @touchstart="handlerStart" 
        @touchmove="handlerMove" 
        @touchend="handlerEnd">
        <li class="content-item" :style="`width:${state.windowWidth}px;`" v-for="(v,k) in titleArr" :key="k">
          <slot :name="'item'+(k+1)"></slot>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  props:{
    titleArr:{
      type: Array,
      default:['首页','list1','list2','list3','list4']
    },
    height:{
      type: String,
      default: ''
    }
  },
  name:'SliderTab',
  data(){
    return{
      current: 0,
      count: 0,
      state:{
        windowWidth: 0,
        lineWidth: 0,
        offset: 0,
        tStart: false
      }
    }
  },
  created(){
    this.getWindow();
  },
  methods:{
    getWindow(){
      this.state.windowWidth = document.documentElement.offsetWidth || document.body.offsetWidth;
      this.count = this.titleArr.length;
      this.state.lineWidth = this.state.windowWidth / this.count;
    },
    select(k){
      this.current = k;
      this.state.offset = this.state.windowWidth * k
    },
    handlerStart(e){
      let {clientX,clientY} = e.changedTouches[0];
      this.startTime = e.timeStamp;
      this.state.tStart = true;
      this.startX = clientX;
      this.tapStartY = clientY;
      this.tapStartX = clientX;
    },
    handlerMove(e){
      let {clientX,clientY} = e.changedTouches[0]
      let offsetX = this.startX - clientX;
      this.startX = clientX;
      this.state.offset += offsetX;
      if(this.state.offset <= 0){
        this.state.offset = 0
      }else if(this.state.offset >= this.state.windowWidth*(this.count-1)){
        this.state.offset = this.state.windowWidth*(this.count-1)
      }
    },
    handlerEnd(e){
      let endTime = e.timeStamp;
      let {clientX,clientY} = e.changedTouches[0];
      //快速滑动
      if(endTime - this.startTime <= 300){
        //向左
        if(Math.abs(this.tapStartY - clientY) < 50) {
          if(Math.abs(this.tapStartX - clientX) < 10) return;
          if(this.tapStartX - clientX > 5) {
            if(this.current < this.count -1) {
              this.current = ++this.current
            }
          } else {
            if(this.current > 0) {
              this.current = --this.current
            }
          }
          this.state.offset = this.state.windowWidth*this.current;
        } else {
          //快速滑动 但是Y距离大于50 所以用户是左右滚动
          let page = Math.round(this.state.offset/this.state.windowWidth);
          if (this.current != page) {
            this.current = page;
          }
          this.state.offset = this.state.windowWidth*page;
        }
      } else {
        let page = Math.round(this.state.offset/this.state.windowWidth);
        if (this.current != page) {
          this.current = page;
        }
        this.state.offset = this.state.windowWidth*page;
      }
      this.state.tStart = false;
    }
  }
}
</script>


# vue-countdown
a cool &amp;&amp; nice  countdown timer component (canvas used) for vue2.x

> canvas实现类似时钟倒计时效果的vue插件。移动端友好。

## show result 效果展示

![Damon风](https://github.com/Damon0820/vue-countdown/blob/master/static/img/show1.gif "Damon风")
</br>[Damon风的blog地址](https://www.cnblogs.com/damonFeng/)

## Installation

### npm
```
npm install vue-canvas-countdown --save-dev
````

### import
````
#### es6
import countDown from 'vue-canvas-countdown'
#### commonjs
const countDown = require('vue-canvas-countdown')
````

### Usage

``` html
<template>
  <div id="app" @click="fireCD">
    <div class="demo">
      <countDown 
        :fire="fire"
        time="15"
        :tiping="tiping"
        :tipend="tipend"
        @onEnd="onEnd"/>
     </div>
  </div>
</template>
```
``` javascript
<script>

import countDown from './components/countDown'
export default {
  name: 'App',
  components: {
    countDown
  },
  data () {
    return {
      fire: 0,
      tiping: {
        text: '倒计时进行中',
        color: '#fff'
      },
      tipend: {
        text: '倒计时结束',
        color: '#fff'
      }
    }
  },
  methods: {
    fireCD () {
      // 配置参数（更多配置如下表）
      this.tiping = {
        text: '请下注',
        color: '#fff'
      }
      this.tipend = {
        text: '停止下注',
        color: '#fff'
      }

      // 启动倒计时(效果如上图所示)      
      this.fire++ 
    },
    onEnd () {
      console.log('倒计时结束的回调函数')
    }
  }
}
</script>
```

### Props 参数
        属性           可选  类型     单位    默认值                   备注

      * fire:         必选  [Number]  px   default: 200        在父组件this.fire++ 即可启动倒计时
      * width,height: 可选  [Number]  px   default: 200 200    设置宽高
      * bgCir:        可选  [String]       rgba(0, 0, 0, .6)   倒计时圆盘背景颜色
      * time:         可选  [Number]  px   default: 15         倒计时所用
      * statusChange: 可选  [Array]        [10000, 500]        倒计时状态改变的时机/时间点（绿=>黄=>红）
      * tiping:       可选  [Object]       {text: '倒计时', color: '#fff'}    倒计时进行时的静态文本内容和颜色(注意：color和text都得设置)
      * tipend:       可选  [Object]       {text: 'END', color: '#fff'}       倒计时结束时的静态文本内容和颜色(注意：color和text都得设置)

### Describe 说明 
  welcome your issue  and commit

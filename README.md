# vue-countdown
a cool &amp;&amp; nice  countdown timer component (canvas used) for vue2.x

> vue + canvas实现炫酷时钟倒计时效果的vue插件。移动端友好。

## show result 效果展示

#### 效果图gif展示
  说明：此gif清晰度很低，因为转成gif图的时候，质量受损，帧数减少，所以倒计时转到红色时候看起来变的很模糊。但是实际在浏览器上效果全程都是很清晰和连贯的

![Damon风](https://github.com/Damon0820/vue-countdown/blob/master/static/img/show1.gif "Damon风")
#### 运动过程的高清截图展示
  说明：由于git图清晰度低，辅助用以下几张高清截图展示效果

<br/>![Damon风](https://github.com/Damon0820/vue-countdown/blob/master/static/img/countdown-1.png "Damon风") ![Damon风](https://github.com/Damon0820/vue-countdown/blob/master/static/img/countdown-2.png "Damon风") ![Damon风](https://github.com/Damon0820/vue-countdown/blob/master/static/img/countdown-3.png "Damon风")
</br>[Damon风的blog地址](https://www.cnblogs.com/damonFeng/)

#### 动画步骤分析：假如设定倒计时总时间为15s， 变黄色时机为10s，变红色时机为5s。
　　1、开始倒计时后颜色为绿色。绿色含义是：倒计时的时间离结束时间还很长。
　　2、10s后变黄色。黄色的含义是：倒计时的时间离结束时间挺近了，起警告作用。动画中，出现快速旋转的扇形。
　　3、5s后变红色。红色的含义是：倒计时的时间马上就要结束了，起强烈警告作用。动画中，快速旋转的扇形速度加快。
　　4、0s倒计时结束。动画消失。静态圆形框中显示提示文字。

## Installation

### npm 下载
```
npm install vue-canvas-countdown --save-dev
````

### import 引入

#### es6
````
import countDown from 'vue-canvas-countdown'
····
#### commonjs
····
const countDown = require('vue-canvas-countdown')
````

### Usage 使用方法

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

import countDown from 'vue-canvas-countdown'
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

      * fire:         必选  [Number]        default: 200                      在父组件this.fire++ 即可启动倒计时
      * width,height: 可选  [Number]  px    default: 200 200                  设置宽高
      * bgCir:        可选  [String]        rgba(0, 0, 0, .6)                 倒计时圆盘背景颜色
      * time:         可选  [Number] 秒/s   default: 15                       倒计时所用
      * statusChange: 可选  [Array] 毫秒/ms [10000, 500]                       倒计时状态改变的时机/时间点（绿=>黄=>红）
      * tiping:       可选  [Object]        {text: '倒计时', color: '#fff'}    倒计时进行时的静态文本内容和颜色(注意：color和text都得设置)
      * tipend:       可选  [Object]        {text: 'END', color: '#fff'}       倒计时结束时的静态文本内容和颜色(注意：color和text都得设置)

### Describe 说明 
  welcome your issue  and commit

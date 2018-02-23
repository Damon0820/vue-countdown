<template>
  <div id="app" @click="fireCD">
    <div class="demo">
      <countDown 
        :fire="fire"
        :time="time"
        :width="width"
        :height="height"
        :tiping="tiping"
        :tipend="tipend"
        :bgCir="bgCir"
        @onEnd="onEnd"/>
     </div>
  </div>
</template>

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
      time: 16,
      width: 200,
      height: 200,
      tiping: {
        text: '倒计时啊',
        color: '#fff'
      },
      tipend: {
        text: '结束了',
        color: '#fff'
      },
      bgCir: 'rgba(0, 0, 0, .6)'
    }
  },
  methods: {
    fireCD () {
      // 自定义参数设置
      this.time = 33
      this.width = 300
      this.height = 300
      this.tiping = 'gogogo'
      this.bgCir = 'rgba(255, 255, 111, .6)'
      this.tiping = {
        text: '倒计了啊',
        color: '#3f3'
      }
      this.tipend = {
        text: 'over',
        color: '#f33'
      }
      // 启动倒计时
      this.fire++
    },
    onEnd () {
      console.log('倒计时结束的回调函数')
    }
  },
  mounted () {
    // 启动倒计时
    this.fire++    
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

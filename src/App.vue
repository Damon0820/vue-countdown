<template>
  <div id="app">
    <div class="demo">
      <countDown
        ref="countDown"
        :fire="fire"
        :time="time"
        :width="width"
        :height="height"
        :tiping="tiping"
        :tipend="tipend"
        :bgCir="bgCir"
        :statusChange="statusChange"
        @onStatusChange="onStatusChange"
        @onEnd="onEnd"/>
      <button @click="fireCD">配置参数开启新的倒计时</button>
    </div>
  </div>
</template>

<script>

// import countDown from 'vue-canvas-countdown'
import countDown from './components/countDown'

export default {
  name: 'App',
  components: {
    countDown
  },
  data () {
    return {
      fire: 0,
      time: 15,
      width: 200,
      height: 200,
      tiping: {
        text: '请下注',
        color: '#fff'
      },
      tipend: {
        text: '停止下注',
        color: '#fff'
      },
      bgCir: 'rgba(0, 0, 0, .6)',
      statusChange: [12000, 5000],
    }
  },
  methods: {
    // 重新开启新的倒计时
    fireCD () {
      // 自定义参数设置
      this.time = 30
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
    onStatusChange (payload) {
      console.log('倒计时状态改变：', payload)
    },
    onEnd () {
      console.log('倒计时结束了')
    }
  },
  mounted () {
    // 启动倒计时
    // 也支持：this.$refs.countDown.startCd()方式 启动倒计时
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
button {
  margin-top: 60px;
}
</style>

<!--
     Describe: 倒计时countDown
 -->
<template>
  <!--公告-->
  <section class="ctD-wrap">
    <canvas id="cD" width="200" height="200">浏览器不支持Canvas,请升级或改用其它浏览器！</canvas>
  </section>
</template>

<script>

  export default {
    data () {
      return {
        ctx: null,
        timer: null,
        sec: 15,      // 倒计时的时间
        ms: 10,
        r: 100,        // 背景圆半径r。这里100为cavans宽的一半
        rB: 90,        // 圆环半径90
        lineWidth: 8,  // 圆环粗细程度
        degFan: Math.PI / 4,  // 扇形大小
        color: ['rgba(0, 255, 0, .6)', 'rgba(0, 100, 0, .6)'],      // 颜色
        speedFan: 12,   // 运动扇形的速度
        cdText: '请下注',   // 倒计时的文本
        cdEndCb: null     // 倒计时结束的回调
      }
    },
    computed: {
      // 在坐标系的度数：逆时针旋转90度
      degCs () {
        // 走了一圈的百分之多少
        let pro = (1 - this.ms / (this.sec * 1000))
        // 走的度数
        let deg = pro * Math.PI * 2
        return deg - Math.PI / 2
      }
    },
    methods: {
      drawText (text, fontSize, x, y) {
        // 文字
        this.ctx.fillStyle = '#fff'
        this.ctx.font = `normal ${fontSize}px ''`
        this.ctx.textAlign = 'center'
        this.ctx.textBaseline = 'middle'
        this.ctx.fillText(text, x, y)
      },
      drawCirBg () {
        // 圆形背景
        this.ctx.beginPath()
        this.ctx.arc(0, 0, this.r, 0, Math.PI * 2, false)
        this.ctx.fillStyle = 'rgba(0, 0, 0, .6)'
        this.ctx.fill()
      },
      drawCirBd () {
        // 圆环
        this.ctx.beginPath()
        this.ctx.arc(0, 0, this.rB, this.degCs, Math.PI * 2 - Math.PI / 2, false)
        this.ctx.strokeStyle = this.color[0]
        this.ctx.stroke()
      },
      drawFan (deg) {
        // 画扇形：
        // 1、半径 this.rB - this.lineWidth / 2；
        // 2、角度大小：this.degFan；
        // 3、位置：deg；
        this.ctx.beginPath()
        this.ctx.moveTo(0, 0)
        this.ctx.arc(0, 0, this.rB - this.lineWidth / 2, deg, deg + this.degFan, false)
        this.ctx.closePath()

        // 渐变：确定渐变起终坐标点
        let x0 = Math.cos(deg) * this.r
        let y0 = Math.sin(deg) * this.r
        let x1 = Math.cos(deg + this.degFan) * this.r
        let y1 = Math.sin(deg + this.degFan) * this.r
        let gradient = this.ctx.createLinearGradient(x0, y0, x1, y1)
        gradient.addColorStop(0, this.color[0])
        gradient.addColorStop(1, this.color[1])
        this.ctx.fillStyle = gradient
        this.ctx.fill()
      },
      colorChange () {
        switch (true) {
          case this.ms >= 10000 :
            this.color[0] = 'rgba(0, 255, 0, .6)'
            this.color[1] = 'rgba(0, 100, 0, .6)'
            break
          case this.ms >= 5000 :
            this.color[0] = 'rgba(255, 255, 0, .6)'
            this.color[1] = 'rgba(100, 100, 0, .6)'
            this.speedFan += 0.01
            break
          case this.ms <= 5000 :
            this.color[0] = 'rgba(255, 0, 0, .6)'
            this.color[1] = 'rgba(100, 0, 0, .6)'
            this.speedFan += 0.02
            break
        }
      },
      clearTimer () {
        clearInterval(this.timer)
        this.timer = null
      },
      clearCavans () {
        this.ctx.clearRect((-1) * this.r, (-1) * this.r, 2 * this.r, 2 * this.r)
      },
      reset () {
        this.speedFan = 12
      },
      // canvas绘图
      drawCd () {
        let self = this
//        this.clearCavans()
        // 原点移动

        this.drawCirBg()

//        console.log(this.sec * 1000, this.ms)
        if (this.ms <= 0) {
          // 转了一圈后
//          this.drawText('停止下注', 40, 0, 0)
          this.clearCavans()
          if (typeof this.cdEndCb === 'function') {
            this.cdEndCb()
          }
          this.clearTimer()
        } else {
          // 一圈之内
          this.ctx.lineWidth = this.lineWidth
          this.colorChange()

          this.drawCirBd()

          this.drawFan(this.degCs)

          // 快速运动的扇形
          if (this.ms < 10000) {
            // 速度不断加快
//            this.speedFan += 0.015
//            console.log(this.speedFan)
            let sDeg = this.degCs * this.speedFan
            this.drawFan(sDeg)
          }

          // 文字
          let time = Math.ceil(this.ms / 1000)
          this.drawText(time, 80, 0, -30)
          this.drawText(this.cdText, 30, 0, 30)
        }
      },
      // 启动定时器，开始倒计时
      // endCb：结束倒计时的回调函数
      startCd () {
        this.reset()
        this.clearCavans()
        this.clearTimer()
        // fps：1s的帧数
        let fps = 30
        let sepTime = 1000 / fps
        this.timer = setInterval(() => {
          this.drawCd()
          this.ms -= sepTime
//          console.log(this.ms)
        }, sepTime)
      },
      // 下注倒计时
      cdBet (cb) {
        this.cdText = '请下注'
        this.cdEndCb = cb
        this.startCd()
      },
      // 咪牌倒计时
      cdMi (cb) {
        this.cdText = '咪牌'
        this.cdEndCb = cb
        this.startCd()
      },
      // 开牌中
      stageTip (msg) {
        this.clearTimer()
        this.clearCavans()
        this.drawCirBg()
        this.drawText(msg, 40, 0, 0)
      }
    },
    mounted () {
      const self = this
      let drawing = document.querySelector('#cD')
      this.ctx = drawing.getContext('2d')
      // 移动原点
      this.ctx.translate(this.r, this.r)

      this.startCd()
    },
    watch: {
      sec: {
        immediate: true,
        handler: function (val) {
          console.log('watch imd')
          this.ms = 1000 * val
        }
      }
    }
  }
</script>

<style scoped>

  .ctD-wrap {
    
  }

  #cD {

  }

</style>


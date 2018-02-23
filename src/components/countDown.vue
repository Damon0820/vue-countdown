<!--
     Describe: canvas实现类似时钟的倒计时效果
 -->
<template>
  <!--公告-->
  <section class="ctD-wrap">
    <canvas class="ctD" ref="ctD" :style="styles" width="200" height="200">浏览器不支持Canvas,请升级或改用其它浏览器！</canvas>
  </section>
</template>

<script>

  // fps：1s的帧数
  const fps = 60
  // 背景圆半径r。这里100为cavans  width的一半
  const r = 100
  // 圆环半径90
  const rB = 90
  // 圆环粗细程度
  const lineWidth = 8
  // 扇形大小
  const degFan = Math.PI / 4
  // 扇形的速度增量step
  const degFanStep = [0.1, 0.15]
  // 初始颜色
  const color = ['rgba(0, 255, 0, .6)', 'rgba(0, 100, 0, .6)']

  const regNumber = /^\d+$/

  // 验证数字类型
  function validatorNumber (defautVal) {
    return {
        validator (val) {
          return regNumber.test(val)
        },
        default: defautVal
      }
  }

  // 验证fontStyle
  function validatorFontStyle (...arg) {
    return {
        default () {
          return {
            text: arg[0],
            color: arg[1]
          }
        },
        validator (val) {
          return val.text && val.color
        }
      }
  }

  export default {
    props: {
      // 开始倒计时
      fire: Object.assign({require: true}, validatorNumber(0)),
      width: validatorNumber(200),
      height: validatorNumber(200),
      // 倒计时的总时间 (s)
      time: validatorNumber(15),
      // 倒计时的状态改变(绿 => 黄 => 红)
      statusChange: {
        type: Array,
        default () {
          return [10000, 5000]
        },
        validator (val) {
          return val.length === 2
        }
      },
      // 倒计时进行时的文本
      tiping: validatorFontStyle('倒计时', '#fff'),
      // 倒计时结束时的文本
      tipend: validatorFontStyle('END', '#fff'),
      // 倒计时圆盘背景颜色
      bgCir: {
        type: String,
        default: 'rgba(0, 0, 0, .6)'
      }
    },
    data () {
      return {
        ctx: null,
        timer: null,
        timeLeft: 0, // 倒计时正在进行的剩余时间
        speedFan: 0,   // 运动扇形的速度
        r,        
        rB,        
        lineWidth,  
        degFan,
        color,
        degFanStep
      }
    },
    computed: {
      // 宽高
      styles () {
        return {
          width: this.width + 'px',
          height: this.height + 'px'
        }
      },
      // 在坐标系的度数：逆时针旋转90度
      degCs () {
        // 走了一圈的百分之多少
        let pro = (1 - this.timeLeft / (this.time * 1000))
        // 走的度数
        let deg = pro * Math.PI * 2
        return deg - Math.PI / 2
      },
    },
    methods: {
      drawText (fontStyle, fontSize, x, y) {
        // 文字
        this.ctx.fillStyle = fontStyle.color
        this.ctx.font = `normal ${fontSize}px ''`
        this.ctx.textAlign = 'center'
        this.ctx.textBaseline = 'middle'
        this.ctx.fillText(fontStyle.text, x, y)
      },
      drawCirBg () {
        // 圆形背景
        this.ctx.beginPath()
        this.ctx.arc(0, 0, this.r, 0, Math.PI * 2, false)
        this.ctx.fillStyle = this.bgCir
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
        // 画扇形：(重点，难点部分)
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
          case this.timeLeft >= this.statusChange[0] :
            this.color[0] = 'rgba(0, 255, 0, .6)'
            this.color[1] = 'rgba(0, 100, 0, .6)'
            break
          case this.timeLeft >= this.statusChange[1] :
            this.color[0] = 'rgba(255, 255, 0, .6)'
            this.color[1] = 'rgba(100, 100, 0, .6)'
            // 运动扇形的速度增量
            this.speedFan += degFanStep[0] * fps / 60
            break
          case this.timeLeft <= this.statusChange[1] :
            this.color[0] = 'rgba(255, 0, 0, .6)'
            this.color[1] = 'rgba(100, 0, 0, .6)'
            // 运动扇形的速度增量            
            this.speedFan += degFanStep[1] * fps / 60
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
        this.timeLeft = 1000 * this.time
      },
      // canvas绘图
      drawCd () {
        this.drawCirBg()

        if (this.timeLeft <= 0) {
          // 倒计时结束,转了一圈后
          this.staticTip()
          this.$emit('onEnd')
        } else {
          // 倒计时正在进行,转了一圈之内
          this.ctx.lineWidth = this.lineWidth
          this.colorChange()

          this.drawCirBd()

          this.drawFan(this.degCs)

          // 快速运动的扇形
          if (this.timeLeft < this.statusChange[0]) {
            // 速度不断加快
            let sDeg = this.speedFan
            this.drawFan(sDeg)
          }

          // 文字
          let time = Math.ceil(this.timeLeft / 1000)
          this.drawText({
            text: time,
            color: this.tiping.color
          }, 80, 0, -30)
          this.drawText(this.tiping, 30, 0, 30)
        }
      },
      // 启动定时器，开始倒计时
      // endCb：结束倒计时的回调函数
      startCd () {
        this.reset()
        this.clearCavans()
        this.clearTimer()
        let sepTime = 1000 / fps
        this.timer = setInterval(() => {
          this.drawCd()
          this.timeLeft -= sepTime
        }, sepTime)
      },
      // 倒计时结束状态展示
      staticTip () {
        this.clearTimer()
        this.clearCavans()
        this.drawCirBg()
        this.drawText(this.tipend, 40, 0, 0)
      }
    },
    mounted () {
      // init
      let drawing = this.$refs.ctD
      this.ctx = drawing.getContext('2d')
      // 移动原点
      this.ctx.translate(this.r, this.r)
    },
    destroyed () {
      this.clearTimer()
    },
    watch: {
      fire () {
        this.startCd()
      }
    }
  }

  // canvas标签内的width和height属性是相对尺寸，用于内部计算使用。
  // 使用css样式，可整体调整canvas的物理大小
</script>

<style scoped>

</style>

<template>
  <div class="box">
    <div ref="slot" class="slot" :style="{ borderRadius: radius + 'px' }">
      <slot></slot>
    </div>
    <canvas
      v-if="width && height"
      class="canvas"
      ref="canvas"
      :width="width"
      :height="height"
    ></canvas>
  </div>
</template>

<script>
export default {
  name: "ScratchCard",
  props: {
    scratchPercent: {
      type: Number,
      default: 80,
    },
    imageUrl: {
      type: String,
    },
    maskColor: {
      type: String,
      default: "#cccccc",
    },
    fillStyle: {
      type: String,
      default: "#000000",
    },
    font: {
      type: String,
      default: "18px Arial",
    },
    text: {
      type: String,
      default: "刮一刮",
    },
    radius: {
      type: Number,
      default: 0,
    },
    scratchRadius: {
      type: Number,
      default: 10,
    },
  },
  data() {
    return {
      ctx: null,
      isScratching: false,
      width: 0,
      height: 0,
    };
  },
  mounted() {
    this.init();
    window.addEventListener("resize", this.onResize);
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.onResize);
  },
  methods: {
    init() {
      this.ctx = null;
      //初始化canvas宽高
      this.initCanvas();
      //dom渲染完成后初始化绘制并绑定事件
      this.$nextTick(() => {
        this.initDraw();
        this.bindEvents();
      });
    },
    //重绘
    onResize() {
      this.ctx = null;
      this.init();
    },
    //初始化显示canvas
    initCanvas() {
      this.width = this.$refs.slot.offsetWidth;
      this.height = this.$refs.slot.offsetHeight;
    },
    //初始化绘制
    initDraw() {
      this.ctx = this.$refs.canvas.getContext("2d");
      this.ctx.globalAlpha = 1;
      this.ctx.fillStyle = this.maskColor;
      //绘制圆角
      if (this.radius) {
        const radius = this.radius;
        //使用clip剪切出圆角
        this.ctx.beginPath();
        //左上角圆角
        this.ctx.moveTo(radius, 0);
        this.ctx.arcTo(0, 0, 0, radius, radius);
        //左下角圆角
        this.ctx.lineTo(0, this.height - radius);
        this.ctx.arcTo(0, this.height, radius, this.height, radius);
        //右下角圆角
        this.ctx.lineTo(this.width - radius, this.height);
        this.ctx.arcTo(
          this.width,
          this.height,
          this.width,
          this.height - radius,
          radius
        );
        //右上角圆角
        this.ctx.lineTo(this.width, radius);
        this.ctx.arcTo(this.width, 0, this.width - radius, 0, radius);
        this.ctx.closePath();
        this.ctx.clip();
      }
      this.ctx.fillRect(0, 0, this.width, this.height);
      //若没有图片绘制文本
      if (!this.imageUrl) {
        // 文本
        this.ctx.fillStyle = this.fillStyle;
        this.ctx.font = this.font;
        // ctx.textAlign = "center";
        // ctx.textBaseline = "middle";

        // 绘制文字
        let text = this.text;
        let textWidth = this.ctx.measureText(text).width;
        let x = this.width / 2 - textWidth / 2;
        let y = this.height / 2 + 6;

        this.ctx.fillText(text, x, y);
      }
      //绘制图片
      if (this.imageUrl) {
        // 创建 Image 对象
        var img = new Image();

        // 设置图片URL
        img.src = this.imageUrl;

        // 监听图片加载完成事件
        img.onload = () => {
          //确定圆角半径

          // 在 Canvas 上绘制图片
          this.ctx.drawImage(img, 0, 0, this.width, this.height);
        };
      }
    },
    bindEvents() {
      // pc
      this.$refs.canvas.addEventListener("mousedown", (e) => {
        this.isScratching = true;
        this.drawArc(e);
      });
      this.$refs.canvas.addEventListener("mousemove", (e) => {
        if (this.isScratching == true) {
          this.drawArc(e);
        }
      });
      this.$refs.canvas.addEventListener("mouseup", () => {
        this.isScratching = false;
        this.calcArea();
      });
      // wap
      this.$refs.canvas.addEventListener("touchstart", (e) => {
        this.$emit("scratchStart");
        this.isScratching = true;
        this.drawArc(e);
      });
      this.$refs.canvas.addEventListener("touchmove", (e) => {
        if (this.isScratching == true) {
          this.drawArc(e);
        }
      });
      this.$refs.canvas.addEventListener("touchend", () => {
        this.isScratching = false;
        this.$emit("scratchEnd");
        this.calcArea();
      });
    },
    // 刮开区域
    drawArc(e) {
      var canvasPos = this.$refs.canvas.getBoundingClientRect();
      var pageScrollTop =
        document.documentElement.scrollTop || document.body.scrollTop;
      var pageScrollLeft =
        document.documentElement.scrollLeft || document.body.scrollLeft;
      var mouseX =
        (e.pageX || e.targetTouches[0].pageX) - canvasPos.left - pageScrollLeft;
      var mouseY =
        (e.pageY || e.targetTouches[0].pageY) - canvasPos.top - pageScrollTop;
      this.ctx.globalCompositeOperation = "destination-out";
      this.ctx.beginPath();
      this.ctx.fillStyle = "white";
      this.ctx.moveTo(mouseX, mouseY);
      this.ctx.arc(mouseX, mouseY, this.scratchRadius, 0, 3 * Math.PI);
      this.ctx.fill();
    },
    // 计算区域
    calcArea() {
      var myImg = this.ctx.getImageData(0, 0, this.width, this.height);
      var num = 0;
      var max = myImg.data.length / 4;
      for (var i = 0; i < myImg.data.length; i += 4) {
        if (myImg.data[i + 3] == 0) {
          num++;
        }
      }
      if (num >= max * (this.scratchPercent / 100)) {
        this.$refs.canvas.remove();
        this.$emit("scratchAll");
      }
    },
  },
};
</script>
<style lang="less" scoped>
.box {
  position: relative;
  display: inline-block;
}
.slot {
  display: inline-block;
  overflow: hidden;
}
.canvas {
  position: absolute;
  top: 0;
  left: 0;
}
</style>
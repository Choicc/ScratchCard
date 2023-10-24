<!--
 * @Descripttion: 
 * @version: 
 * @Author: Choicc
 * @Date: 2023-03-17 11:15:13
 * @LastEditors: Choicc
 * @LastEditTime: 2023-03-22 16:15:21
 * @email: ccyforworks@gmail.com
-->
<template>
  <div>
    <scratch-card ref="scratchCardRef" maskColor="skyblue" fillStyle="red" font="30px 微软雅黑" text="刮一刮文字"
      :imageUrl="imageUrl" :radius="5" :scratchRadius="20" :scratchPercent="80" @scratchStart="scratchStart"
      @scratchEnd="scratchEnd" @scratchAll="scratchAll">
      <!-- 自定义奖品内容插槽 -->
      <div class="prize">{{ prize }}</div>
    </scratch-card>
    <div class="btn-reset" @click="reset">重置</div>
  </div>
</template>

<script>
import ScratchCard from "@/components/ScratchCard";
export default {
  name: "ScratchCardDemo",
  components: {
    ScratchCard,
  },
  data() {
    return {
      imageUrl: require("./assets/thumb-1920-642551.png"),
      prize: "我的奖品",
    };
  },
  methods: {
    scratchStart() {
      console.log("scratchStart");
    },
    scratchEnd() {
      console.log("scratchEnd");
    },
    scratchAll() {
      console.log("scratchAll");
    },
    reset() {
      console.log("reset");
      this.prize = "重置后我的奖品";
      this.$refs.scratchCardRef.reset();
    }
  },
};
</script>
<style scoped>
* {
  margin: 0;
  padding: 0;
}

.prize {
  width: 80vw;
  height: 45vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background: lightcoral;
}

.btn-reset {
  display: block;
  width: 10vw;
  height: 5w;
  line-height: 5w;
  text-align: center;
  border-radius: 10%;
  border: 1px solid black;
}
</style>
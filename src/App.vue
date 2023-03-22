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
    <scratch-card
      maskColor="skyblue"
      fillStyle="red"
      font="30px 微软雅黑"
      text="刮一刮文字"
      :imageUrl="imageUrl"
      :radius="5"
      :scratchRadius="20"
      :scratchPercent="80"
      @scratchStart="scratchStart"
      @scratchEnd="scratchEnd"
      @scratchAll="scratchAll"
    >
      <!-- 自定义奖品内容插槽 -->
      <div class="prize">我的奖品</div>
    </scratch-card>
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
</style>
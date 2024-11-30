<template>
  <div class="hamburger-icon">
    <div v-for="(text, index) in texts" :key="index" class="line-container">
      <div class="line">
        <span class="line-text" :style="{ marginLeft: `${getTextOffset(text)}%` }">
          <span style="font-size: 18px; color: pink">测试测试</span>
          <span class="number" :style="{ backgroundColor: bgColors[index] }">
            {{ formatNumber(text) }}
          </span>
          <span class="dot" />
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const props = defineProps({
  texts: {
    type: Array,
    default: () => [836822, 87, 299345],
  },
});

const bgColors = ["blue", "purple", "red"];

const formatNumber = (num) => {
  return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
};
// 修正方法，计算文本的偏移量
const getTextOffset = (num) => {
  const maxNumber = Math.max(...props.texts);
  const lineLength = 50; // 横线的长度，可以根据实际情况调整
  const offset = (num / maxNumber) * lineLength;
  return Math.min(offset, lineLength); // 确保偏移量不超过横线长度
};
</script>

<style scoped>
.hamburger-icon {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 100%;
  height: 60px;
  transition: all 0.3s ease-in-out;
}

.line-container {
  margin: 20px 0;
}

.line {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 2px;
  background-color: #808080; /* 灰色 */
  border-radius: 1px;
  transition: all 0.3s ease-in-out;
}

.line-text {
  position: absolute;
  z-index: 1;
  display: flex;
  align-items: center;
  padding: 0 5px;
  font-size: 14px;
  color: white;
  transition: all 0.3s ease-in-out;
}

.dot {
  width: 8px;
  height: 8px;
  margin-left: 10px;
  background-color: white;
  border-radius: 50%;
}

.number {
  line-height: 1;
}
</style>

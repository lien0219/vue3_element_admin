<template>
  <el-popover placement="bottom" :width="300" trigger="click">
    <template #reference>
      <el-button>插入表情</el-button>
    </template>
    <div class="emoji-grid">
      <button v-for="emoji in emojis" :key="emoji" class="emoji-button" @click="selectEmoji(emoji)">
        {{ emoji }}
      </button>
    </div>
  </el-popover>
</template>

<script setup>
import { ElPopover, ElButton } from "element-plus";

const emojis = ["😀", "😂", "😍", "🤔", "😎", "👍", "👎", "❤️", "🎉", "🔥"];

const emit = defineEmits(["select"]);

const selectEmoji = (emoji) => {
  emit("select", emoji);
};
</script>

<style scoped>
.emoji-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 5px;
}

.emoji-button {
  padding: 5px;
  font-size: 24px;
  cursor: pointer;
  background: none;
  border: none;
}

.emoji-button:hover {
  background-color: #f0f0f0;
}
</style>

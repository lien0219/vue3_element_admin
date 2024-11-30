<template>
  <el-card :body-style="{ padding: '0px', backgroundColor: backgroundColor }" class="custom-card">
    <div class="card-header">
      <i :class="iconClass" class="icon" />
      <div class="title">{{ title }}</div>
      <div class="actions">
        <el-button type="text" @click="edit">编辑</el-button>
        <el-button type="text" @click="remove">移除</el-button>
      </div>
    </div>
    <slot />
    <!-- 用于放置卡片内容 -->
  </el-card>
</template>

<script setup>
import { defineProps, defineEmits } from "vue";

// 定义组件的属性
const props = defineProps({
  icon: String, // 小图标的类名
  title: String, // 标题文字
  backgroundColor: String, // 卡片背景色
});

// 定义事件发射器
const emit = defineEmits(["edit", "remove"]);

// 编辑按钮事件
const edit = () => {
  emit("edit");
};

// 移除按钮事件
const remove = () => {
  emit("remove");
};

// 将图标类名转换为Element Plus的图标类名
const iconClass = props.icon ? `el-icon-${props.icon}` : "";
</script>

<style scoped>
.custom-card {
  width: 100%;
}

.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
}

.icon {
  font-size: 1.5em;
}

.title {
  flex-grow: 1;
  text-align: center;
}

.actions {
  display: flex;
  gap: 10px;
}
</style>

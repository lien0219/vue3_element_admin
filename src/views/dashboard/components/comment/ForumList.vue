<template>
  <div class="forum-list">
    <h1>论坛列表</h1>
    <el-card v-for="forum in forums" :key="forum.id" class="forum-item">
      <template #header>
        <h2>{{ forum.title }}</h2>
      </template>
      <p>{{ forum.description }}</p>
      <CommentSection :forumId="forum.id" />
    </el-card>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { ElCard } from "element-plus";
import CommentSection from "./CommentSection.vue";

const forums = ref([
  { id: 1, title: "论坛1", description: "这是第一个论坛的描述" },
  { id: 2, title: "论坛2", description: "这是第二个论坛的描述" },
]);
</script>

<style scoped>
.forum-list {
  max-width: 800px;
  margin: 0 auto;
}

.forum-item {
  margin-bottom: 20px;
}
</style>

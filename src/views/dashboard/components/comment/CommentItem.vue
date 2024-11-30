<template>
  <div class="comment-item">
    <div class="comment-content">
      <strong>{{ comment.author }}</strong>
      <span class="timestamp">{{ formatDate(comment.timestamp) }}</span>
      <p v-html="formatContent(comment.content)" />
    </div>
    <div class="comment-actions">
      <el-button type="text" @click="$emit('like', comment.id)">
        点赞 ({{ comment.likes }})
      </el-button>
      <el-button type="text" @click="showReplyInput = true">回复</el-button>
      <el-button v-if="isOwnComment" type="text" @click="$emit('delete', comment.id)">
        删除
      </el-button>
    </div>
    <div v-if="showReplyInput" class="reply-input">
      <el-input v-model="replyContent" placeholder="回复评论..." />
      <el-button type="primary" @click="submitReply">发送回复</el-button>
    </div>
    <div v-if="comment.replies && comment.replies.length > 0" class="replies">
      <CommentItem
        v-for="reply in comment.replies"
        :key="reply.id"
        :comment="reply"
        @reply="$emit('reply', comment.id, $event)"
        @like="$emit('like', $event)"
        @delete="$emit('delete', $event)"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { ElButton, ElInput } from "element-plus";

const props = defineProps(["comment"]);
const emit = defineEmits(["reply", "like", "delete", "preview-image"]);

const showReplyInput = ref(false);
const replyContent = ref("");

const isOwnComment = computed(() => {
  // 这里应该检查评论是否属于当前用户
  return props.comment.author === "Current User";
});

const formatDate = (timestamp) => {
  return new Date(timestamp).toLocaleString();
};

const formatContent = (content) => {
  // 将图片标记转换为实际的图片标签
  return content.replace(
    /\[image:(.*?)\]/g,
    '<img src="$1" class="comment-image" @click="previewImage(\'$1\')" />'
  );
};

const submitReply = () => {
  if (replyContent.value.trim()) {
    emit("reply", props.comment.id, replyContent.value);
    replyContent.value = "";
    showReplyInput.value = false;
  }
};

const previewImage = (url) => {
  emit("preview-image", url);
};
</script>

<style scoped>
.comment-item {
  padding-bottom: 15px;
  margin-bottom: 15px;
  border-bottom: 1px solid #eee;
}

.comment-content {
  margin-bottom: 10px;
}

.timestamp {
  margin-left: 10px;
  font-size: 0.8em;
  color: #999;
}

.comment-actions {
  display: flex;
  gap: 10px;
}

.reply-input {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.replies {
  margin-top: 10px;
  margin-left: 20px;
}

.comment-image {
  max-width: 200px;
  max-height: 200px;
  cursor: pointer;
}
</style>

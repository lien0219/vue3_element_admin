<template>
  <div class="comment-section">
    <h3>评论</h3>
    <el-input v-model="newComment" type="textarea" :rows="3" placeholder="写下你的评论..." />
    <div class="comment-actions">
      <EmojiPicker @select="addEmoji" />
      <ImageUploader @upload="addImage" />
      <el-button type="primary" @click="submitComment">发送评论</el-button>
    </div>
    <div class="comments-list">
      <CommentItem
        v-for="comment in comments"
        :key="comment.id"
        :comment="comment"
        @reply="replyToComment"
        @like="likeComment"
        @delete="deleteComment"
        @preview-image="showPreview"
      />
    </div>
    <ImagePreview :visible="previewVisible" :url="previewUrl" @close="closePreview" />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { ElInput, ElButton } from "element-plus";
import EmojiPicker from "./EmojiPicker.vue";
import ImageUploader from "./ImageUploader.vue";
import CommentItem from "./CommentItem.vue";
import ImagePreview from "./ImagePreview.vue";

const props = defineProps(["forumId"]);

const newComment = ref("");
const comments = ref([]);
const previewVisible = ref(false);
const previewUrl = ref("");

const addEmoji = (emoji) => {
  newComment.value += emoji;
};

const addImage = (imageUrl) => {
  newComment.value += `[image:${imageUrl}]`;
};

const submitComment = () => {
  if (newComment.value.trim()) {
    const comment = {
      id: Date.now(),
      content: newComment.value,
      author: "Current User", // 这里应该使用实际的用户信息
      timestamp: new Date().toISOString(),
      likes: 0,
      replies: [],
    };
    comments.value.unshift(comment);
    newComment.value = "";
  }
};

const replyToComment = (parentId, content) => {
  const parentComment = comments.value.find((c) => c.id === parentId);
  if (parentComment) {
    parentComment.replies.push({
      id: Date.now(),
      content,
      author: "Current User", // 这里应该使用实际的用户信息
      timestamp: new Date().toISOString(),
      likes: 0,
    });
  }
};

const likeComment = (commentId) => {
  const comment = comments.value.find((c) => c.id === commentId);
  if (comment) {
    comment.likes++;
  }
};

const deleteComment = (commentId) => {
  comments.value = comments.value.filter((c) => c.id !== commentId);
};

const closePreview = () => {
  previewVisible.value = false;
  previewUrl.value = "";
};

const showPreview = (url) => {
  previewUrl.value = url;
  previewVisible.value = true;
};

onMounted(() => {
  // 这里应该从API获取评论数据
  // 为了演示,我们使用模拟数据
  comments.value = [
    {
      id: 1,
      content: "这是一条评论",
      author: "User1",
      timestamp: "2023-05-20T12:00:00Z",
      likes: 5,
      replies: [],
    },
    {
      id: 2,
      content: "这是另一条评论 [image:https://example.com/image.jpg]",
      author: "User2",
      timestamp: "2023-05-20T13:00:00Z",
      likes: 3,
      replies: [
        {
          id: 3,
          content: "这是一条回复",
          author: "User3",
          timestamp: "2023-05-20T14:00:00Z",
          likes: 1,
        },
      ],
    },
  ];
});
</script>

<style scoped>
.comment-section {
  margin-top: 20px;
}

.comment-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
  margin-bottom: 20px;
}

.comments-list {
  margin-top: 20px;
}
</style>

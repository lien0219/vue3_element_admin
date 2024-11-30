<template>
  <el-upload action="#" :auto-upload="false" :on-change="handleChange" :show-file-list="false">
    <el-button>上传图片</el-button>
  </el-upload>
</template>

<script setup>
import { ElUpload, ElButton } from "element-plus";

const emit = defineEmits(["upload"]);

const handleChange = (file) => {
  const reader = new FileReader();
  reader.onload = (e) => {
    emit("upload", e.target.result);
  };
  reader.readAsDataURL(file.raw);
};
</script>

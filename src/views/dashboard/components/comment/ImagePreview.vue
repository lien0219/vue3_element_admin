<template>
  <el-dialog
    :visible="visible"
    title="图片预览"
    @update:visible="$emit('update:visible', $event)"
    @close="$emit('close')"
  >
    <img :src="url" style="max-width: 100%; max-height: 80vh" />
  </el-dialog>
</template>

<script setup>
import { ElDialog } from "element-plus";

defineProps({
  visible: Boolean,
  url: String,
});

defineEmits(["update:visible", "close"]);
</script>

<template>
    <el-upload class="upload-demo" drag action="/api/upload-video" accept=".mp4">
        <el-icon class="el-icon--upload"><upload-filled /></el-icon>
        <div class="el-upload__text">
            Drop file here or <em>click to upload</em>
        </div>
    </el-upload>
</template>
  
<script setup lang="js">
import { UploadFilled } from '@element-plus/icons-vue'
</script>
  
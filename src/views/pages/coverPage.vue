<template>
  <div class="image-page">
    <img :src="coverImg" alt="page image" />
    <div class="overlay">
      <h1 class="title">돋멘</h1>
    </div>
  </div>
</template>

<script setup>
import coverImg from "../image/corver.jpg";

defineProps({
  src: {
    type: String,
    required: true
  }
})
</script>

<style scoped>
.image-page {
  position: relative; /* ⭐ 필수 */
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: #f5f1e8;
}

.image-page img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* 🔥 텍스트 overlay */
.overlay {
  position: absolute;
  inset: 0;
  background: none;
  
  display: flex;
  justify-content: center;
  align-items: center;

  color: white;
  text-align: center;
}

/* 제목 */
.title {
  font-size: 48px;
  font-weight: bold;
  text-shadow: 
    0 4px 10px rgba(0,0,0,0.6),
    0 0 20px rgba(0,0,0,0.4);
}
</style>
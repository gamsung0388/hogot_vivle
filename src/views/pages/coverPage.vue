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
  position: relative; 
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: white;
}

.image-page img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

/* 🔥 텍스트 overlay */
.overlay {
  position: absolute;
  top: 10px;
  left: 50%;
  transform: translateX(-50%);

  width: auto;        /* ⭐ 중요 */
  height: auto;       /* ⭐ 중요 */
  padding: 0;         /* ⭐ 혹시 있으면 제거 */
  background: none;   /* ⭐ 확실히 제거 */

  pointer-events: none; /* ⭐ 클릭 방해 방지 */
}

/* 제목 */
.title {
  font-size: 72px;
  font-weight: 800;
  letter-spacing: 6px;

  color: white;

  text-shadow:
    0 4px 12px rgba(0,0,0,0.6),
    0 0 30px rgba(0,0,0,0.3);
}
</style>
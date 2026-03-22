<template>
  <div class="player">
    <!-- audio -->
    <audio ref="bgm" @ended="onEnded"></audio>

    <!-- 컨트롤 -->
    <button @click="togglePlay">
      {{ isPlaying ? '⏸' : '▶' }}
    </button>

    <!-- 시간 -->
    <div class="time">
      {{ formatTime(currentTime) }} / {{ formatTime(duration) }}
    </div>

    <!-- 진행바 -->
    <div class="progress-bar" @click="seek">
      <div class="progress" :style="{ width: progress + '%' }"></div>
    </div>

    <!-- 볼륨 -->
    <input type="range" min="0" max="1" step="0.01" v-model="volume" />
  </div>
</template>
<script setup>

import { ref, onMounted, computed, watch } from 'vue'

const bgm = ref(null)

const playlist = [
  `${import.meta.env.BASE_URL}assets/audio/track1.mp3`,
  `${import.meta.env.BASE_URL}assets/audio/track2.mp3`
]

let currentIndex = 0

const isPlaying = ref(false)
const currentTime = ref(0)
const duration = ref(0)
const volume = ref(0.25)

watch(volume, (v) => {
  bgm.value.volume = v
})

// ▶ 재생
const playTrack = async() => {
  bgm.value.src = playlist[currentIndex]
  await bgm.value.load()

  try {
    await bgm.value.play()
    isPlaying.value = true
  } catch {}
}

const safePlay = async () => {
  try {
    await bgm.value.play()
    isPlaying.value = true
  } catch (e) {
    console.log(e)
  }
}

// ⏸ 토글
const togglePlay = async () => {
    if (!bgm.value) return  // ⭐ 이거 추가

    // ⭐ 처음이면 트랙 넣기
    if (!bgm.value.src) {
        bgm.value.src = playlist[currentIndex]
    }

    if (bgm.value.paused) {
        await safePlay()
    } else {
        bgm.value.pause()
        isPlaying.value = false
    }
}

// ⏭ 다음 곡
const onEnded = () => {
  currentIndex = (currentIndex + 1) % playlist.length
  playTrack()
}

const startAudio = async () => {
  if (isPlaying.value) return

  if (!bgm.value.src) {
    bgm.value.src = playlist[currentIndex]
  }

  try {
    await bgm.value.play()
    isPlaying.value = true
  } catch (e) {
    console.log(e)
  }
}

// ⏱ 시간 업데이트
onMounted(() => {
    bgm.value.volume = volume.value

    bgm.value.addEventListener('timeupdate', () => {
      currentTime.value = bgm.value.currentTime
      
    })

    bgm.value.addEventListener('loadedmetadata', () => {
        duration.value = bgm.value.duration
    })
    
    // ⭐ 핵심 추가
    document.addEventListener('click', startAudio, { once: true })
    document.addEventListener('touchstart', startAudio, { once: true })
})


// ⏳ 진행률
const progress = computed(() => {
  if (!duration.value) return 0
  return (currentTime.value / duration.value) * 100
})

// ⏩ seek
const seek = (e) => {
  const rect = e.currentTarget.getBoundingClientRect()
  const clickX = e.clientX - rect.left
  const ratio = clickX / rect.width

  bgm.value.currentTime = ratio * duration.value
}

// ⏱ 포맷
const formatTime = (time) => {
  const min = Math.floor(time / 60)
  const sec = Math.floor(time % 60)
  return `${min}:${sec.toString().padStart(2, '0')}`
}

defineExpose({
  play: () => bgm.value?.play()
})

</script>
<style scoped>
.player {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);

  display: flex;
  align-items: center;
  gap: 10px;

  padding: 10px 16px;
  border-radius: 12px;

  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(6px);

  color: white;
}

/* 버튼 */
button {
  background: none;
  border: none;
  color: white;
  font-size: 18px;
  cursor: pointer;
}

/* 진행바 */
.progress-bar {
  width: 200px;
  height: 6px;
  background: rgba(255,255,255,0.2);
  border-radius: 4px;
  cursor: pointer;
}

.progress {
  height: 100%;
  background: white;
  border-radius: 4px;
}

/* 시간 */
.time {
  font-size: 12px;
}

/* 볼륨 */
input[type="range"] {
  width: 80px;
}
</style>
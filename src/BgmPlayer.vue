<template>
  <div class="player">
    <div class="title-wrapper">
      <div class="title">
        {{ currentTrack.title }} &nbsp;&nbsp;&nbsp;
        {{ currentTrack.title }}
      </div>
    </div>
    <!-- audio -->
    <audio ref="bgm" @ended="onEnded"></audio>

    <!-- 이전곡 -->
    <button @click="prevTrack">◀◀</button>

    <!-- 컨트롤 -->
    <button @click="togglePlay">
      {{ isPlaying ? '⏸' : '▶' }}
    </button>

    <!-- 다음곡 -->
    <button @click="nextTrack">▶▶</button>
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
  {
    title: "바람에 몸을 맡기리", 
    src: `${import.meta.env.BASE_URL}assets/audio/track1.mp3`,
  },
  {
    title: "Grand Golden Hodod", 
    src: `${import.meta.env.BASE_URL}assets/audio/track2.mp3`,
  },  
]

let currentIndex = ref(0)
const audio = ref(null);
const currentTrack = computed(() => playlist[currentIndex.value]);


const isPlaying = ref(false)
const currentTime = ref(0)
const duration = ref(0)
const volume = ref(0.25)

watch(volume, (v) => {
  bgm.value.volume = v
})

// ▶ 재생
const playTrack = async() => {
  bgm.value.volume = 0
  bgm.value.src = currentTrack.value.src

  await bgm.value.play()

  let v = 0
  const fade = setInterval(() => {
    v += 0.05
    if (v >= volume.value) {
      bgm.value.volume = volume.value
      clearInterval(fade)
    } else {
      bgm.value.volume = v
    }
  }, 50)
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
  if (!bgm.value) return

  if (!bgm.value.src) {
    bgm.value.src = currentTrack.value.src
  }

  if (bgm.value.paused) {
    await bgm.value.play()
    isPlaying.value = true
  } else {
    bgm.value.pause()
    isPlaying.value = false
  }
}

// ⏭ 다음 곡
const onEnded = () => {
  nextTrack()
}

const startAudio = async () => {
  if (isPlaying.value) return

  if (!bgm.value.src) {
    bgm.value.src = currentTrack.value.src
  }

  try {
    await bgm.value.play()
    isPlaying.value = true
  } catch (e) {
    console.log(e)
  }
}

function prevTrack() {
  currentIndex.value =
    (currentIndex.value - 1 + playlist.length) % playlist.length
  playTrack()
}

function nextTrack() {
  currentIndex.value = (currentIndex.value + 1) % playlist.length
  playTrack()
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

.title-wrapper {
  width: 200px;
  overflow: hidden;
  position: relative;
}

.title {
  white-space: nowrap;
  display: inline-block;
  animation: marquee 8s linear infinite;
}

@keyframes marquee {
  0%   { transform: translateX(0); }
  100% { transform: translateX(-100%); }
}

.player {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);

  display: flex;
  align-items: center;
  gap: 12px;

  padding: 10px 16px;
  border-radius: 12px;

  background: rgba(0,0,0,0.6);
  color: white;
}

@media (max-width: 768px) {
  .title-wrapper {
    width: 140px;
  }
}
</style>
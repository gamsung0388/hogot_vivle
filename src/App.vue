<template>
  <div ref="book" class="book">
    <div
      v-for="(page, index) in pages"
      :key="index"
      class="page"
    >
      <component :is="page.component" v-bind="page.props" />
    </div>
  </div>
  <BgmPlayer ref="playerRef" />
  <div class="mobile-nav" v-if="isMobile">
    <button @click="pageFlip.flipPrev()">←</button>
    <button @click="pageFlip.flipNext()">→</button>
  </div>
</template>

<script setup>
import { onMounted, ref, watch } from 'vue'
import { PageFlip } from 'page-flip'

import coverPage from './views/pages/coverPage.vue'
import Chapter1 from './views/pages/Chapter1.vue'
import Chapter2 from './views/pages/Chapter2.vue'
import Chapter3 from './views/pages/Chapter3.vue'
import Chapter4 from './views/pages/Chapter4.vue'
import Chapter5 from './views/pages/Chapter5.vue'
import Chapter6 from './views/pages/Chapter6.vue'
import Chapter7 from './views/pages/Chapter7.vue'
import Chapter8 from './views/pages/Chapter8.vue'
import Chapter9 from './views/pages/Chapter9.vue'
import Chapter10 from './views/pages/Chapter10.vue'

import BgmPlayer from './BgmPlayer.vue'

const book = ref(null)

const pages = [
  { component: coverPage},
  { component: Chapter1},
  { component: Chapter2},
  { component: Chapter3},
  { component: Chapter4},
  { component: Chapter5},
  { component: Chapter6},
  { component: Chapter7},
  { component: Chapter8},
  { component: Chapter9},
  { component: Chapter10},
]



onMounted(() => {  
    
  // 모바일 대응
  const isMobile = window.innerWidth < 768;

  console.log(book.value.querySelectorAll('.page'))

  const pageFlip = new PageFlip(book.value, {
    width: isMobile ? 350 : 1000,
    height: isMobile ? 600 : 800,
    
    minWidth: 300,
    maxWidth: 1000,

    minHeight: 400,
    maxHeight: 1200,

    showCover: false,

    usePortrait: true,
    mobileScrollSupport: true
  })

  window.addEventListener("resize", () => {
    const isMobile = window.innerWidth < 768;

    pageFlip.update({
      width: isMobile ? 350 : 800,
      height: isMobile ? 600 : 600,
      usePortrait: isMobile
    });
  });

  pageFlip.loadFromHTML(
    document.querySelectorAll(".page")
  )

})





</script>
<style scoped>
  .book {
    width: 100%;
    height: 100vh;
    margin: 0 auto;
  }
  .nav {
    position: fixed;
    bottom: 30px;
    right: 40px;
  }

  .nav button {
    background: rgba(0,0,0,0.6);
    color: white;
    border: none;
    margin: 0 5px;
    padding: 10px 14px;
    border-radius: 8px;
  }

  @media (max-width: 768px) {
    .book {
      height: 100vh;
    }

    .stf__parent {
      transform: scale(1) !important;
    }
  }
</style>
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
</template>

<script setup>
import { onMounted, ref } from 'vue'
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
  
  console.log(book.value.querySelectorAll('.page'))

  const pageFlip = new PageFlip(book.value, {
    width: 900,
    height: 900,
    showCover: false,
    usePortrait: true,
  })

  pageFlip.loadFromHTML(
    book.value.querySelectorAll('.page')
  )
})
</script>
<style scoped>
  .book {
    width: 900px;
    height: 900px;
    margin: 0 auto;
  }
</style>
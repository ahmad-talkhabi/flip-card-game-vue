<script setup>
import {computed, ref, watch} from "vue";
import {shuffle} from '../utils/array.js'
import FlipGameItem from './FlipGameItem.vue'
import image01 from '../assets/images/product-1.jpg'
import image02 from '../assets/images/product-2.jpg'
import image03 from '../assets/images/product-3.jpg'
import image04 from '../assets/images/product-4.jpg'
import image05 from '../assets/images/product-5.jpg'
import image06 from '../assets/images/product-6.jpg'
import image07 from '../assets/images/product-7.jpg'
import image08 from '../assets/images/product-8.jpg'

const COUNT = 40
const MAX_TIME_SECONDS = 2 * 60
const DEFAULT_VALUES = {
  loading: false,
  selectedItem: null,
  count: COUNT,
  timer: MAX_TIME_SECONDS,
  timeout: null,
  message: '',
}
const list = [
  {
    id: 1,
    img: image01,
  },
  {
    id: 2,
    img: image02,
  },
  {
    id: 3,
    img: image03,
  },
  {
    id: 4,
    img: image04,
  },
  {
    id: 5,
    img: image05,
  },
  {
    id: 6,
    img: image06,
  },
  {
    id: 7,
    img: image07,
  },
  {
    id: 8,
    img: image08,
  },
]

const gameItems = ref([])
const playData = ref(DEFAULT_VALUES)

const timer = computed(() => {
  if (!playData.value.timer) return '00:00'
  return new Date(playData.value.timer * 1000).toISOString().substring(14, 19)
})
const isWinner = computed(() => gameItems.value.every(i => i.detect))

function startNewGame() {

  if (playData.value.loading) return
  if (playData.value.timeout) {
    clearTimeout(playData.value.timeout)
  }

  playData.value = {
    ...DEFAULT_VALUES,
    loading: true,
  }

  const shuffleList = shuffle(list.concat(list))

  gameItems.value = shuffleList.map((item, index) => ({
    ...item,
    index: index + 1,
    show: true,
    detect: false,
  }))

  setTimeout(() => {
    gameItems.value.forEach(i => i.show = false)
    playData.value.loading = false
    countDownTimer()
  }, 3000)
}

function showItem(item) {
  if (
      playData.value.loading ||
      playData.value.count < 1 ||
      playData.value.timer < 1 ||
      item.index === playData.value.selectedItem?.index
  ) return false

  playData.value.loading = true
  item.show = true

  if (!playData.value.selectedItem) {
    playData.value.loading = false
    return playData.value.selectedItem = item
  }

  playData.value.count--

  if (playData.value.selectedItem.id === item.id) {
    gameItems.value.filter(i => i.id === item.id).map(i => i.detect = true)
  }

  setTimeout(() => {
    hideAll()
    playData.value.loading = false
  }, 1000)

  playData.value.selectedItem = null
}

function hideAll() {
  gameItems.value.forEach(i => i.show = false)
}

function countDownTimer() {
  if (playData.value.timeout) {
    clearTimeout(playData.value.timeout)
  }
  if (playData.value.timer > 0) {
    playData.value.timeout = setTimeout(() => {
      playData.value.timer -= 1
      countDownTimer()
    }, 1000)
  } else {
    clearTimeout(playData.value.timeout)
  }
}

function pauseGame() {
  clearTimeout(playData.value.timeout)
  playData.value.loading = false
}

watch(() => playData.value.timer, (val) => {
  if (val === 0 && !isWinner.value) {
    playData.value.message = 'وقتت تموم شد :( میخوای دوباره بازی کنی؟'
    pauseGame()
  }
})
watch(() => playData.value.count, () => {
  if (playData.value.count < 1 && !isWinner.value) {
    playData.value.message = 'حرکت هات تموم شد :( میخوای دوباره بازی کنی؟'
    pauseGame()
  }
})
watch(isWinner, (val) => {
  if (val) {
    playData.value.message = 'آفرین برنده شدی :) میخوای دوباره بازی کنی؟'
    pauseGame()
  }
})

</script>

<template>
  <div class="text-xl font-black">
    <div v-if="gameItems.length" class="flex mb-5 justify-between">
      <div>
        <span>تعداد حرکت: </span>
        <span>{{ playData.count }}</span>
      </div>
      <div>
        <span>زمان: </span>
        <span>{{ timer }}</span>
      </div>
    </div>

    <div class="grid grid-cols-4 gap-2 mb-4">
      <FlipGameItem
          v-for="(item) in gameItems"
          :key="item.index"
          :item="item"
          @show="showItem(item)"
      />
    </div>

    <div class="text-sm text-center h-[20px] mb-4">{{ playData.message }}</div>

    <div class="flex">
      <button
          class="!bg-amber-200 rounded-md text-black"
          :disabled="playData.loading"
          @click="startNewGame"
      >
        <span v-if="gameItems.length">شروع دوباره</span>
        <span v-else>شروع بازی</span>
      </button>
    </div>
  </div>
</template>

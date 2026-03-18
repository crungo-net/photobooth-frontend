<template>
  <div v-show="showBox" id="countdown-timer-container" class="flex flex-center" style="width: 70vw; height: 70vh">
    <!-- eslint-disable-next-line vue/no-v-html -->
    <div v-show="showMessage" id="countdown-timer-message" style="position: absolute; font-size: 150px"
      v-html="messageText"></div>
    <svg v-show="showCountdown && !showMessage" viewBox="0 0 200 300" style="height: 70vh; overflow: visible">
      <defs>
        <linearGradient id="countdown-gradient" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#AC5410" />
          <stop offset="47.1%" stop-color="#FF8F00" />
          <stop offset="100%" stop-color="#FFE592" />
        </linearGradient>

        <!-- White drop shadow: offset right 6, down 10 -->
        <filter id="white-shadow" x="-20%" y="-20%" width="150%" height="150%">
          <feFlood flood-opacity="0" result="BackgroundImageFix" />
          <feColorMatrix in="SourceAlpha" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0"
            result="hardAlpha" />
          <feOffset dx="6" dy="10" />
          <feComposite in2="hardAlpha" operator="out" />
          <feColorMatrix type="matrix" values="0 0 0 0 1 0 0 0 0 1 0 0 0 0 1 0 0 0 1 0" />
          <feBlend mode="normal" in2="BackgroundImageFix" result="effect1_dropShadow" />
          <feBlend mode="normal" in="SourceGraphic" in2="effect1_dropShadow" result="shape" />
        </filter>

        <!-- Black drop shadow: offset right 4, down 8 -->
        <filter id="black-shadow" x="-20%" y="-20%" width="150%" height="150%">
          <feFlood flood-opacity="0" result="BackgroundImageFix" />
          <feColorMatrix in="SourceAlpha" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0"
            result="hardAlpha" />
          <feOffset dx="4" dy="8" />
          <feComposite in2="hardAlpha" operator="out" />
          <feColorMatrix type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0" />
          <feBlend mode="normal" in2="BackgroundImageFix" result="effect1_dropShadow" />
          <feBlend mode="normal" in="SourceGraphic" in2="effect1_dropShadow" result="shape" />
        </filter>
      </defs>

      <!-- White border with white drop shadow -->
      <g opacity="0.7" filter="url(#white-shadow)">
        <text x="100" y="230" text-anchor="middle" font-size="280" font-weight="900"
          font-family="'Kumbh Sans', 'Inter', sans-serif" fill="url(#countdown-gradient)" stroke="white"
          stroke-width="10" stroke-linejoin="round" paint-order="stroke">
          {{ displayNumber }}
        </text>
      </g>

      <!-- Black outline (no filter, renders cleanly on top) -->
      <text x="100" y="230" text-anchor="middle" font-size="280" font-weight="900"
        font-family="'Kumbh Sans', 'Inter', sans-serif" fill="none" stroke="black"
        stroke-width="10" stroke-linejoin="round">
        {{ displayNumber }}
      </text>

      <!-- Black drop shadow only -->
      <text filter="url(#black-shadow)" x="100" y="230" text-anchor="middle" font-size="280" font-weight="900"
        font-family="'Kumbh Sans', 'Inter', sans-serif" fill="url(#countdown-gradient)" stroke="none">
        {{ displayNumber }}
      </text>
    </svg>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

let intervalTimerId: number
const remainingSeconds = ref(0)
const startDuration = ref(0)

interface Props {
  duration: number
  messageDuration?: number
  messageText?: string
}

const props = withDefaults(defineProps<Props>(), {
  messageDuration: 0.5,
  messageText: '😃',
})

const displayNumber = computed(() => {
  return parseFloat(remainingSeconds.value.toFixed(0))
})

const showBox = computed(() => {
  return remainingSeconds.value > 0
})
const showCountdown = computed(() => {
  return remainingSeconds.value > 0
})
const showMessage = computed(() => {
  return remainingSeconds.value <= props.messageDuration
})

onMounted(() => {
  startTimer()
})

onBeforeUnmount(() => {
  abortTimer()
})

const abortTimer = () => {
  clearInterval(intervalTimerId)
  remainingSeconds.value = 0
}
const startTimer = () => {
  startDuration.value = props.duration
  remainingSeconds.value = startDuration.value

  console.log(`starting timer, duration=${startDuration.value}`)

  intervalTimerId = window.setInterval(() => {
    remainingSeconds.value -= 0.05

    if (remainingSeconds.value <= 0) {
      clearInterval(intervalTimerId)
    }
  }, 50)
}
</script>

<style lang="css">
#countdown-timer-container {
  pointer-events: none;
  user-select: none;
}
</style>

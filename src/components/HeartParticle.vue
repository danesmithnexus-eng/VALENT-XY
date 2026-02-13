<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  x: Number,
  y: Number,
  size: Number,
  speed: Number,
  color: String
})

const emit = defineEmits(['remove'])

const currentY = ref(props.y)
const currentX = ref(props.x)
const opacity = ref(1)
const rotation = ref(Math.random() * 360)
let animationId

onMounted(() => {
  const animate = () => {
    currentY.value -= props.speed
    currentX.value += Math.sin(currentY.value / 50) * 2
    rotation.value += 2
    
    if (currentY.value < -100) {
      emit('remove')
      return
    }
    
    animationId = requestAnimationFrame(animate)
  }
  
  animationId = requestAnimationFrame(animate)
})

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
})
</script>

<template>
  <div 
    class="heart-particle"
    :style="{
      left: currentX + 'px',
      top: currentY + 'px',
      width: size + 'px',
      height: size + 'px',
      opacity: opacity,
      transform: `rotate(${rotation}deg)`
    }"
  >
    <svg viewBox="0 0 24 24" :fill="color" xmlns="http://www.w3.org/2000/svg">
      <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"/>
    </svg>
  </div>
</template>

<style scoped>
.heart-particle {
  position: fixed;
  pointer-events: none;
  z-index: 100;
}

.heart-particle svg {
  width: 100%;
  height: 100%;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.2));
}
</style>

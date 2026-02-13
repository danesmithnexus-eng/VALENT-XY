<script setup>
import { ref, computed } from 'vue'
import HeartParticle from './components/HeartParticle.vue'

const noButtonClicks = ref(0)
const noButtonPosition = ref({ x: 0, y: 0 })
const showBlush = ref(false)
const showForcedValentine = ref(false)
const hearts = ref([])
const noButtonText = computed(() => {
  if (noButtonClicks.value >= 7) return "You have no choice"
  return "No STFU"
})
const thankYouButtonText = computed(() => {
  if (showForcedValentine.value) return "You have no choice, I am your valentine"
  return "Thank you"
})

const generateHearts = (count = 30) => {
  for (let i = 0; i < count; i++) {
    setTimeout(() => {
      hearts.value.push({
        id: Date.now() + i,
        x: Math.random() * window.innerWidth,
        y: window.innerHeight + 50,
        size: Math.random() * 30 + 20,
        speed: Math.random() * 3 + 2,
        color: ['#ff6b9d', '#ff8fab', '#ffc2d1', '#ff006e', '#fb6f92'][Math.floor(Math.random() * 5)]
      })
    }, i * 50)
  }
}

const removeHeart = (id) => {
  hearts.value = hearts.value.filter(h => h.id !== id)
}

const handleThankYou = () => {
  showBlush.value = true
  generateHearts(40)
}

const handleNoButton = () => {
  noButtonClicks.value++
  
  // Move button to random position on every click (before 7th click)
  if (noButtonClicks.value < 7) {
    const maxX = window.innerWidth - 150
    const maxY = window.innerHeight - 80
    noButtonPosition.value = {
      x: Math.random() * maxX,
      y: Math.random() * maxY
    }
  }
  
  // On 7th click, show forced valentine
  if (noButtonClicks.value >= 7) {
    showForcedValentine.value = true
    generateHearts(60)
  }
}
</script>

<template>
  <div class="valentine-container">
    <!-- Moving GIF Background -->
    <div class="gif-background"></div>

    <!-- Floating Hearts Background -->
    <div class="hearts-container">
      <HeartParticle 
        v-for="heart in hearts" 
        :key="heart.id"
        :x="heart.x"
        :y="heart.y"
        :size="heart.size"
        :speed="heart.speed"
        :color="heart.color"
        @remove="removeHeart(heart.id)"
      />
    </div>

    <!-- Main Content Card -->
    <div class="card">
      <div class="window-header">
        <div class="window-controls">
          <span class="dot minimize"></span>
          <span class="dot maximize"></span>
          <span class="dot close"></span>
        </div>
        <span class="heart-icon">✕</span>
      </div>

      <div class="content">
        <h1 class="title">Happy Valentines pretty</h1>
        
        <!-- Blush Reaction -->
        <div v-if="showBlush" class="reaction blush-reaction">
          <div class="face">
            <span class="blush">ACKKK</span>
            <span class="eyes">&gt;////&lt;</span>
            <span class="mouth">‿</span>
          </div>
        </div>

        <!-- Forced Valentine Message -->
        <div v-else-if="showForcedValentine" class="forced-message">
          <div class="face angry-face">
            <span class="eyes">> <</span>
            <span class="mouth">‿</span>
          </div>
          <p class="forced-text">You have no choice, I will still be your valentine</p>
        </div>

        <!-- Default Face -->
        <div v-else class="face default-face">
          <span class="eyes">◠ ◠</span>
          <span class="mouth">‿</span>
        </div>

        <!-- Buttons -->
        <div v-if="!showBlush && !showForcedValentine" class="buttons">
          <button class="btn thank-you" @click="handleThankYou">
            <span class="btn-icon">💕</span>
            {{ thankYouButtonText }}
          </button>
          <button 
            v-if="noButtonClicks === 0"
            class="btn no-button" 
            @click="handleNoButton"
          >
            <span class="btn-icon">💢</span>
            {{ noButtonText }}
          </button>
        </div>

        <!-- Reset Button for after reactions -->
        <button 
          v-if="showBlush || showForcedValentine" 
          class="btn reset-btn"
          @click="() => { showBlush = false; showForcedValentine = false; noButtonClicks = 0; hearts = []; }"
        >
          💝 Start Over
        </button>
      </div>
    </div>

    <!-- Floating No Button (when moved) -->
    <button 
      v-if="noButtonClicks > 0 && noButtonClicks < 7 && !showBlush && !showForcedValentine"
      class="btn no-button floating-btn" 
      @click="handleNoButton"
      :style="{
        left: noButtonPosition.x + 'px',
        top: noButtonPosition.y + 'px'
      }"
    >
      <span class="btn-icon">💢</span>
      {{ noButtonText }}
    </button>

    <!-- Credits -->
    <footer class="credits">
      Created by Xyci using Vue and his interest for you
    </footer>
  </div>
</template>

<style scoped>
.valentine-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #e0c3fc 0%, #8ec5fc 100%);
  position: relative;
  overflow: hidden;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.gif-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 200%;
  height: 100%;
  background: url('/Aibjz6eeT.gif') repeat-x;
  background-size: contain;
  animation: moveLeft 20s linear infinite;
  z-index: 0;
  opacity: 0.3;
  pointer-events: none;
}

@keyframes moveLeft {
  0% {
    transform: translateX(-50%);
  }
  100% {
    transform: translateX(0);
  }
}

.hearts-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
}

.card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  box-shadow: 
    0 10px 40px rgba(147, 112, 219, 0.3),
    0 0 0 4px rgba(255, 182, 193, 0.5);
  width: 90%;
  max-width: 450px;
  overflow: hidden;
  z-index: 10;
  animation: float 3s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.window-header {
  background: linear-gradient(90deg, #a8edea 0%, #fed6e3 100%);
  padding: 12px 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 3px solid #c084fc;
}

.window-controls {
  display: flex;
  gap: 8px;
}

.dot {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  border: 2px solid #9333ea;
}

.dot.minimize { background: #bae6fd; }
.dot.maximize { background: #fbcfe8; }
.dot.close { background: #ddd6fe; }

.heart-icon {
  font-size: 20px;
  color: #ff9ecd;
  font-weight: bold;
}

.content {
  padding: 40px 30px;
  text-align: center;
}

.title {
  color: #9333ea;
  font-size: 2rem;
  margin-bottom: 30px;
  text-shadow: 2px 2px 0 #fbcfe8;
  font-weight: 700;
}

.face {
  font-size: 4rem;
  margin: 30px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.eyes {
  display: block;
  letter-spacing: 8px;
  line-height: 1;
  font-size: 3rem;
}

.mouth {
  display: block;
  margin-top: -15px;
  line-height: 1;
  font-size: 2.5rem;
}

.default-face {
  color: #c084fc;
}

.blush-reaction .face {
  color: #ec4899;
  animation: shake 0.5s ease-in-out;
}

.blush {
  font-size: 1.5rem;
  color: #ec4899;
  font-weight: bold;
  animation: pulse 1s ease-in-out infinite;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-10px) rotate(-5deg); }
  75% { transform: translateX(10px) rotate(5deg); }
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.forced-message {
  margin: 20px 0;
}

.angry-face {
  color: #dc2626;
  animation: angryShake 0.3s ease-in-out infinite;
}

@keyframes angryShake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-3px); }
  75% { transform: translateX(3px); }
}

.forced-text {
  font-size: 1.5rem;
  color: #dc2626;
  font-weight: bold;
  margin-top: 15px;
  animation: fadeIn 0.5s ease-in;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.buttons {
  display: flex;
  gap: 20px;
  justify-content: center;
  margin-top: 30px;
  flex-wrap: wrap;
}

.btn {
  padding: 15px 30px;
  border-radius: 50px;
  border: 3px solid;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
  font-family: inherit;
}

.btn-icon {
  font-size: 1.3rem;
}

.thank-you {
  background: linear-gradient(135deg, #fbcfe8 0%, #f9a8d4 100%);
  border-color: #ec4899;
  color: #831843;
}

.thank-you:hover {
  transform: scale(1.05);
  box-shadow: 0 5px 20px rgba(236, 72, 153, 0.4);
}

.no-button {
  background: linear-gradient(135deg, #ddd6fe 0%, #c4b5fd 100%);
  border-color: #8b5cf6;
  color: #5b21b6;
}

.no-button:hover {
  transform: scale(1.05);
  box-shadow: 0 5px 20px rgba(139, 92, 246, 0.4);
}

.reset-btn {
  background: linear-gradient(135deg, #bae6fd 0%, #7dd3fc 100%);
  border-color: #0ea5e9;
  color: #0c4a6e;
  margin: 20px auto 0;
}

.reset-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 5px 20px rgba(14, 165, 233, 0.4);
}

.credits {
  margin-top: 30px;
  color: #7c3aed;
  font-size: 0.9rem;
  font-weight: 500;
  text-shadow: 1px 1px 0 rgba(255, 255, 255, 0.5);
  z-index: 10;
}

.floating-btn {
  position: fixed;
  z-index: 1000;
}
</style>

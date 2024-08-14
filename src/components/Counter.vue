<template>
  <div class="counter-container">
    <p class="intro-text">
      Let's see how high we can collectively get this number. Clicks since 8/13/2024:
    </p>
    <div class="counter">
      {{ count }}
    </div>
    <button class="deep-button" @click="incrementCount">Click Me</button>

    <!-- Emoji Explosion Container -->
    <div v-if="showEmojiExplosion" class="emoji-explosion-container">
      <span v-for="emoji in emojis" :key="emoji.id" class="emoji">{{ emoji.symbol }}</span>
    </div>

    <!-- Error Modal -->
    <div v-if="showErrorModal" class="error-modal">
      <p>{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
      showEmojiExplosion: false,
      emojis: [], // Array to hold emojis
      showErrorModal: false, // Control visibility of the error modal
      errorMessage: '' // The error message to display
    }
  },
  methods: {
    incrementCount() {
      this.count += 1

      // Check for special numbers
      if (this.count % 100 === 0) {
        this.triggerEmojiExplosion('💯')
      } else if (this.count % 100 === 69) {
        this.triggerEmojiExplosion('👌')
      }
    },
    triggerEmojiExplosion(emoji) {
      // Add the specific emoji to the array
      this.emojis = this.generateEmojis(emoji)
      this.showEmojiExplosion = true

      // Hide the explosion after some time
      setTimeout(() => {
        this.showEmojiExplosion = false
      }, 1500)
    },
    generateEmojis(emoji) {
      return Array.from({ length: 30 }, (_, i) => ({
        id: i,
        symbol: emoji
      }))
    }
  }
}
</script>

<style scoped>
.counter-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background-color: #f5f5f5;
}

.intro-text {
  font-size: 1.2em;
  margin-bottom: 10px;
  text-align: center;
  color: #333;
}

.counter {
  font-size: 2em;
  margin-bottom: 20px;
}

.deep-button {
  touch-action: manipulation;
  width: 150px;
  height: 150px;
  border-radius: 50%;
  background-color: #6200ea;
  color: white;
  border: none;
  outline: none;
  font-size: 1.5em;
  cursor: pointer;
  position: relative;
  transition:
    box-shadow 0.2s ease,
    transform 0.2s ease;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
}

.deep-button:active {
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  transform: translateY(10px);
}

.emoji-explosion-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  z-index: 9999;
}

.emoji {
  position: absolute;
  font-size: 2em;
  animation: explode 1.5s ease forwards;
}

@keyframes explode {
  0% {
    transform: translateY(0) scale(1);
    opacity: 1;
  }
  100% {
    transform: translateY(calc(100vh + 100px)) scale(0.5);
    opacity: 0;
  }
}
</style>

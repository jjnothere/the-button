<template>
  <div class="counter-container">
    <p class="intro-text">
      Let's see how high we can collectively get this number. Clicks since 8/13/2024:
    </p>
    <div
      class="counter"
      :class="{ 'animate-bounce': animateBounce }"
      @animationend="resetAnimation"
    >
      {{ count }}
    </div>
    <button class="deep-button" @click="incrementCount">Click Me</button>

    <!-- Confetti Explosion -->
    <ConfettiExplosion v-if="showConfetti" @animationend="resetConfetti" />

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
import axios from 'axios'
import ConfettiExplosion from 'vue-confetti-explosion'

export default {
  components: {
    ConfettiExplosion
  },
  data() {
    return {
      count: 0,
      socket: null,
      token: '', // Store the token
      showErrorModal: false, // Control visibility of the error modal
      errorMessage: '', // The error message to display
      animateBounce: false, // Control the bounce animation
      showConfetti: false, // Control the confetti explosion
      showEmojiExplosion: false, // Control the emoji explosion
      emojis: [] // Array to hold emojis
    }
  },
  created() {
    this.fetchToken() // Fetch the token when the component is created
    this.fetchCount()
    this.connectWebSocket()
  },
  methods: {
    async fetchToken() {
      try {
        const response = await axios.get('/api/token')
        this.token = response.data.token // Store the token
      } catch (error) {
        console.error('Error fetching token:', error)
      }
    },
    async fetchCount() {
      try {
        const response = await axios.get('/api/count')
        this.count = response.data.count
      } catch (error) {
        console.error('Error fetching count:', error)
      }
    },
    async incrementCount() {
      try {
        const response = await axios.post(
          '/api/increment',
          {},
          {
            headers: {
              'x-access-token': this.token // Include the token in the request headers
            }
          }
        )
        this.count = response.data.count

        // Trigger animation, confetti, and emoji explosion based on count
        if (this.count % 100 === 0) {
          this.animateBounce = true
          this.showConfetti = true
          this.triggerEmojiExplosion('💯')
        } else if (this.count % 100 === 69) {
          this.animateBounce = true
          this.triggerEmojiExplosion('👌')
        }
      } catch (error) {
        console.error('Error incrementing count:', error)
        this.showErrorModalWithMessage(error.response.data.error || 'An error occurred')
      }
    },
    connectWebSocket() {
      const protocol = window.location.protocol === 'https:' ? 'wss' : 'ws'
      const host = window.location.host
      const socketUrl = `${protocol}://${host}`

      this.socket = new WebSocket(socketUrl)

      this.socket.onmessage = (event) => {
        const data = JSON.parse(event.data)
        this.count = data.count
      }

      this.socket.onclose = () => {
        console.log('WebSocket connection closed')
      }
    },
    resetAnimation() {
      // Reset the animation class after it finishes
      this.animateBounce = false
    },
    resetConfetti() {
      // Hide confetti after the animation ends
      this.showConfetti = false
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
    },
    showErrorModalWithMessage(message) {
      this.errorMessage = message
      this.showErrorModal = true
      setTimeout(() => {
        this.showErrorModal = false
      }, 3000) // Hide after 3 seconds
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
  transition: transform 0.5s ease; /* Smooth transition for the animation */
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

/* Bounce animation */
@keyframes bounce {
  0%,
  20%,
  50%,
  80%,
  100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-30px);
  }
  60% {
    transform: translateY(-15px);
  }
}

.animate-bounce {
  animation: bounce 1s ease;
}

/* Emoji explosion container */
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

/* Error modal styling */
.error-modal {
  position: fixed;
  top: 20%;
  left: 50%;
  transform: translateX(-50%);
  background-color: #e88780;
  color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  z-index: 10000;
  font-size: 1.2em;
  text-align: center;
}
</style>

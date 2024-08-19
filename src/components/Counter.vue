<template>
  <div class="counter-container">
    <p class="intro-text">
      Let's see how high we can collectively get this number. Clicks since 8/13/2024:
    </p>
    <div
      class="counter"
      :class="{ 'animate-spin-pop': animateSpinPop }"
      @animationend="resetAnimation"
    >
      {{ count }}
    </div>
    <button class="deep-button" @click="incrementCount">Click Me</button>

    <!-- Error Modal -->
    <div v-if="showErrorModal" class="error-modal">
      <p>{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import JSConfetti from 'js-confetti'

export default {
  data() {
    return {
      count: 0,
      socket: null,
      showErrorModal: false,
      errorMessage: '',
      jsConfetti: null,
      animateSpinPop: false // New state for spinning and popping out the number
    }
  },
  created() {
    this.jsConfetti = new JSConfetti()
    this.fetchCount()
    this.connectWebSocket()
  },
  methods: {
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
        const response = await axios.post('/api/increment')
        this.count = response.data.count

        // Trigger animation, confetti, and emoji explosion based on count
        if (this.count % 10000 === 0) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis([
            '🎉',
            '🥳',
            '💥',
            '🎆',
            '🎇',
            '🎊',
            '✨',
            '💫',
            '🔥',
            '🎁',
            '🎂',
            '🎈',
            '🎀',
            '🎉',
            '🍾',
            '🎺',
            '🎤',
            '🎵',
            '🎶',
            '🎷',
            '🎸',
            '🎹',
            '🎻',
            '🥁',
            '🎯',
            '🎳',
            '🎮',
            '🏆',
            '🏅',
            '🎖️',
            '🎗️',
            '🏵️',
            '🏮',
            '🎐',
            '🎏',
            '🎎',
            '🎟️',
            '🎫',
            '🕺',
            '💃',
            '🕯️',
            '🎋',
            '🎍',
            '🎠',
            '🎡',
            '🎢',
            '🎨',
            '🎭',
            '🎰',
            '🎲',
            '🎴',
            '🃏',
            '🎮'
          ])
        } else if (this.count % 1000 === 0) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis(['🎉', '🥳', '🎊', '🎆', '🎇'])
        } else if (this.count % 100 === 0) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis(['💯', '🎉'])
        } else if (this.count % 100 === 69) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis(['👌', '🔥'])
        } else if (this.count.toString().includes('6969')) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis(['👌', '🔥', '😎', '💯'])
        } else if (this.count.toString().includes('1776')) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis([
            '🇺🇸',
            '🎆',
            '🎇',
            '🗽',
            '🎉',
            '🎊',
            '🍔',
            '🌭',
            '🥧',
            '🍻',
            '🎈',
            '🎯',
            '🎺'
          ])
        } else if (this.count.toString().includes('808')) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis([
            '🥁',
            '🎶',
            '🎵',
            '🎸',
            '🎷',
            '🎺',
            '🎤',
            '🎼',
            '🎧',
            '🪘',
            '🎻',
            '🎹',
            '🎙️'
          ])
        } else if (this.count.toString().includes('420')) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis(['🚬', '💨', '🔥', '💭', '😮‍💨', '💯', '🌿'])
        } else if (this.count.toString().includes('666')) {
          this.triggerSpinPop()
          this.showConfettiWithEmojis(['😈', '👹', '👺', '🔥', '👿', '💀', '☠️'])
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
    triggerSpinPop() {
      this.animateSpinPop = true
      setTimeout(() => {
        this.animateSpinPop = false
      }, 1000) // Duration of the spin and pop-out effect
    },
    showConfettiWithEmojis(emojis) {
      this.jsConfetti.addConfetti({
        emojis: emojis,
        confettiRadius: 12, // Increase the size of the emojis
        confettiNumber: 100 // Increase the number of emojis
      })
    },
    showErrorModalWithMessage(message) {
      this.errorMessage = message
      this.showErrorModal = true
      setTimeout(() => {
        this.showErrorModal = false
      }, 3000)
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
  transition: background-color 0.5s ease; /* Smooth transition */
}

/* Other existing styles */

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

.animate-spin-pop {
  animation: spinPop 1s ease;
}

@keyframes spinPop {
  0% {
    transform: scale(1) rotate(0deg);
  }
  50% {
    transform: scale(2) rotate(360deg); /* Scale to 2x size and rotate */
  }
  100% {
    transform: scale(1) rotate(720deg); /* Return to normal size and complete 2 spins */
  }
}

.deep-button {
  touch-action: manipulation;
  width: 200px;
  height: 200px;
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
  user-select: none; /* Prevent text selection */
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

<template>
  <div class="counter-container">
    <div class="counter">{{ count }}</div>
    <button class="deep-button" @click="incrementCount">Click Me</button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      count: 0,
      socket: null
    }
  },
  created() {
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
      } catch (error) {
        console.error('Error incrementing count:', error)
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
</style>

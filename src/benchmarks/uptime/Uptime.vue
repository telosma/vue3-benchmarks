<template>
  <div id="app">
    <p>FPS: {{ state.fps }}</p>
    <button @click="toggle">{{ state.playing ? 'pause' : 'play' }}</button>
    <UptimeServer
      v-for="server in state.servers"
      :key="server.name"
      :name="server.name"
      :days="server.days"
    >
    </UptimeServer>
  </div>
</template>

<script lang="ts">
import UptimeServer from './UptimeServer.vue'
import { reactive } from 'vue'
export default {
  name: 'Uptime',
  components: {
    UptimeServer
  },
  setup() {
    const state = reactive({
      fps: 0,
      playing: false,
      servers: Object.freeze(generateServers()),
      timeoutId: null,
      lastFrame: null,
      fpsMeter: {
        alpha: 2 / 121,
        lastValue: null,
        push(dataPoint) {
          if (this.lastValue) {
            return (this.lastValue =
              this.lastValue + this.alpha * (dataPoint - this.lastValue))
          } else {
            return (this.lastValue = dataPoint)
          }
        }
      }
    })
    function generateServer(name: string) {
      let days = []
      for (let i = 0; i <= 364; i++) {
        let up = Math.random() > 0.2
        days.push({ number: i, up })
      }
      return { name, days }
    }
    function generateServers() {
      return [
        generateServer("Stefan's Server"),
        generateServer("Godfrey's Server"),
        generateServer("Yehuda's Server")
      ]
    }
    function update() {
      let thisFrame = window.performance.now()
      if (state.lastFrame) {
        state.fps = Math.round(state.fpsMeter.push(1000 / (thisFrame - state.lastFrame)))
      }
      state.servers = Object.freeze(generateServers())
      state.timeoutId = setTimeout(update, 0) // not using rAF because that limits us to 60fps!
      state.lastFrame = thisFrame
    }
    function toggle() {
      state.playing = !state.playing
      if (state.playing) {
        update()
      } else {
        clearTimeout(state.timeoutId)
      }
    }
    return {
      state,
      toggle
    }
  }
}
</script>

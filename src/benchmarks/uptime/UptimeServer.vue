<template>
  <div class="server-uptime">
    <h1>{{ state.name }}</h1>
    <h2>{{ state.upDays }} Days Up</h2>
    <h2>Biggest Streak: {{ state.maxStreak }}</h2>
    <div class="days">
      <UptimeDay v-for="day in days" :key="day.number" :day="day"> </UptimeDay>
    </div>
  </div>
</template>

<script lang="ts">
import UptimeDay from './UptimeDay.vue'
import { computed, reactive } from 'vue'
export default {
  components: { UptimeDay },
  props: ['name', 'days'],
  setup(props) {
    const state = reactive({
      days: props.days,
      name: props.name,
      upDays: computed(() => {
        return props.days.reduce(function (upDays, day) {
          return upDays += (day.up ? 1 : 0)
        }, 0)
      }),
      maxStreak: computed(() => {
        let streak = props.days.reduce(
          ([max, streak], day) => {
            if (day.up && streak + 1 > max) {
              return [streak + 1, streak + 1]
            } else if (day.up) {
              return [max, streak + 1]
            } else {
              return [max, 0]
            }
          },
          [0, 0]
        )
        return streak.max
      })
    })
    return {
      state
    }
  }
}
</script>

<style>
.server-uptime {
  display: block;
  overflow: hidden;
  margin: 0 auto;
  width: 50%;
}
.server-uptime + .server-uptime {
  margin: 20px auto 0 auto;
  border-top: 1px solid #999;
}
.days {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
</style>

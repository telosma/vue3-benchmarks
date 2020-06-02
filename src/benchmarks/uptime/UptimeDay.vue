<template>
  <div class="uptime-day">
    <span class="uptime-day-status" :class="day.up ? '-opera' : '-alert'">
    </span>
    <span class="hover">{{
      day.number + ': ' + day.up ? 'Servers operational!' : 'Red alert!'
    }}</span>
  </div>
</template>

<script lang="ts">
import { reactive } from 'vue'
interface Props {
  day?: object
}
export default {
  props: ['day'],
  setup(props: Props) {}
}
</script>

<style>
.uptime-day {
  display: flex;
}
span.uptime-day-status {
  width: 10px;
  height: 10px;
  margin: 1px;
}
.uptime-day-status.-opera {
  background-color: #8cc665;
}
.uptime-day-status.-alert {
  background-color: #cccccc;
}
.hover {
  display: none;
}
.uptime-day-status:hover + .hover {
  display: flex;
  position: absolute;
  margin-top: -35px;
  margin-left: -30px;
  border-radius: 4px;
  color: #eee;
  background-color: #333;
  padding: 10px;
  font-size: 11px;
}
</style>

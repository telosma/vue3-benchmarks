<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <big-table />
  <!-- <UpTime /> -->
</template>

<script>
// import UpTime from './benchmarks/uptime/UpTime.vue';
import BigTable from './benchmarks/big-table/BigTable.vue';

export default {
  name: 'App',
  components: {
    BigTable,
    // UpTime,
  },
};
</script>

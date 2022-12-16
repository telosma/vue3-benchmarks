<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <big-table />
  </div>
</template>

<script>
import BigTable from './benchmarks/big-table/BigTable.vue';

export default {
  name: 'App',
  components: {
    BigTable,
  },
};
</script>

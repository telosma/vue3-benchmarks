<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <!-- <HelloWorld msg="Hello Vue 3.0 + Vite" /> -->
  <big-table />
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import BigTable from './benchmarks/big-table/BigTable.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld
    BigTable
  }
}
</script>

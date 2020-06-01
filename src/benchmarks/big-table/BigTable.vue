<template>
  <div id="el">
    <div style="margin: 10px">
      <strong>Rows: </strong><input type="text" v-model="state.rows" />
      <strong style="padding-left: 20px">Cols: </strong><input type="text" v-model="state.cols" />
    </div>
    <p>
      <span
        >{{ state.rows }} x {{ state.cols }},
        {{ state.optimized ? 'with' : 'without' }} optimization.
        {{ state.msg }}</span
      >
    </p>

    <p>
      <button v-if="state.optimized" @click="loadBase">
        Disable optimization
      </button>
      <button v-else @click="loadOptimized">
        Enable optimization (Object.freeze)
      </button>
      <button @click="unmount">Unmount</button>
      <button @click="rerender">Rerender with fresh data</button>
    </p>

    <form>
      <strong>Filter Data</strong>:
      <input type="text" v-model="state.filter" />

      <!--
          If the user is filtering the data, we want to offer some insight into
          the breadth of the filtering.
        -->
      <span v-if="state.filter">
        &mdash; Filtering <strong>{{ state.filter }}</strong> over
        {{ state.dataPoints }} data points, {{ visibleCount() }} found.
      </span>
    </form>

    <table width="100%" cellspacing="2" :class="{ filtered: state.filter }">
      <tr v-for="(row, index) in state.grid" :key="`row-${index}`">
        <th>{{ row.id }}</th>
        <td
          v-for="(item, key) in row.items"
          class="item"
          :class="{ hidden: !matches(item) }"
          :key="`item${key}`"
        >
          {{ item.value }}
        </td>
      </tr>
    </table>
  </div>
</template>

<script lang="ts">
import { reactive } from 'vue'
const ROWS = 1000
const COLS = 10
const OPTIMIZED = window.location.hash === '#optimized'
export default {
  name: 'BigTable',
  setup() {
    function generateGrid(rowCount, columnCount) {
      let valuePoints = [
        'Daenerys',
        'Jon',
        'Sansa',
        'Arya',
        'Stannis',
        'Gregor',
        'Tyrion',
        'Theon',
        'Joffrey',
        'Ramsay',
        'Cersei',
        'Bran',
        'Margaery',
        'Melisandre',
        'Daario',
        'Jamie',
        'Eddard',
        'Myrcella',
        'Robb',
        'Jorah',
        'Petyr',
        'Tommen',
        'Sandor',
        'Oberyn',
        'Drogo',
        'Ygritte'
      ]
      let valueIndex = 0
      let grid = []
      for (let r = 0; r < rowCount; r++) {
        let row = {
          id: r,
          items: []
        }
        for (let c = 0; c < columnCount; c++) {
          row.items.push({
            id: r + '-' + c,
            value: valuePoints[valueIndex]
          })
          if (++valueIndex >= valuePoints.length) {
            valueIndex = 0
          }
        }
        grid.push(row)
      }
      return OPTIMIZED ? Object.freeze(grid) : grid
    }
    let grid = generateGrid(ROWS, COLS)
    let s = window.performance.now()
    const state = reactive({
      cols: COLS,
      rows: ROWS,
      optimized: OPTIMIZED,
      msg: 'loading...',
      grid: grid,
      dataPoints: grid.length * grid[0].items.length,
      filter: ''
    })
    window.onhashchange = function () {
      window.location.reload()
    }
    function matches(item) {
      return item.value.toLowerCase().indexOf(state.filter.toLowerCase()) > -1
    }
    function visibleCount() {
      let count = 0
      let grid = state.grid
      for (let i = 0, l = grid.length; i < l; i++) {
        let row = grid[i].items
        for (let j = 0, k = row.length; j < k; j++) {
          let item = row[j]
          let matched = !state.filter || matches(item)
          if (matched) {
            count++
          }
        }
      }
      return count
    }
    function loadBase() {
      window.location.hash = ''
    }
    function loadOptimized() {
      window.location.hash = '#optimized'
    }
    function unmount() {
      console.profile('unmount')
      let s = window.performance.now()
      state.grid = []
      setTimeout(function () {
        state.msg =
          'umount took: ' + (window.performance.now() - s).toFixed(2) + 'ms'
        console.profileEnd('unmount')
      }, 0)
    }
    function rerender() {
      let grid = generateGrid(state.rows, state.cols)
      let s = window.performance.now()
      console.profile('rerender')
      state.grid = grid
      setTimeout(function () {
        state.msg =
          'rerender took: ' + (window.performance.now() - s).toFixed(2) + 'ms'
        console.profileEnd('rerender')
      }, 0)
    }
    setTimeout(function () {
      state.msg = 'initial render took: ' + (window.performance.now() - s).toFixed(2) + 'ms'
    }, 0)
    return {
      state,
      matches,
      rerender,
      unmount,
      loadBase,
      loadOptimized,
      visibleCount
    }
  }
}
</script>

<style scoped>
@import './style.css';
</style>

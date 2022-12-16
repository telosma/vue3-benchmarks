<template>
  <div id="el">
    <div style="margin: 10px">
      <strong>Rows: </strong><input type="text" v-model="rows" />
      <strong style="padding-left: 20px">Cols: </strong
      ><input type="text" v-model="cols" />
    </div>
    <p>
      <span
        >{{ rows }} x {{ cols }},
        {{ optimized ? 'with' : 'without' }} optimization. {{ msg }}</span
      >
    </p>

    <p>
      <button v-if="optimized" @click="loadBase">Disable optimization</button>
      <button v-else @click="loadOptimized">
        Enable optimization (Object.freeze)
      </button>
      <button @click="unmount">Unmount</button>
      <button @click="rerender">Rerender with fresh data</button>
    </p>

    <form>
      <strong>Filter Data</strong>:
      <input type="text" v-model="filter" />

      <!--
          If the user is filtering the data, we want to offer some insight into
          the breadth of the filtering.
        -->
      <span v-if="filter">
        &mdash; Filtering <strong>{{ filter }}</strong> over
        {{ dataPoints }} data points, {{ visibleCount() }} found.
      </span>
    </form>

    <table width="100%" cellspacing="2" :class="{ filtered: filter }">
      <tr v-for="(row, index) in grid" :key="`row-${index}`">
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

<script>
const ROWS = 1000;
const COLS = 10;
const OPTIMIZED = window.location.hash === '#optimized';

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
    'Ygritte',
  ];
  let valueIndex = 0;
  let grid = [];
  for (let r = 0; r < rowCount; r++) {
    let row = {
      id: r,
      items: [],
    };
    for (let c = 0; c < columnCount; c++) {
      row.items.push({
        id: r + '-' + c,
        value: valuePoints[valueIndex],
      });
      if (++valueIndex >= valuePoints.length) {
        valueIndex = 0;
      }
    }
    grid.push(row);
  }
  return OPTIMIZED ? Object.freeze(grid) : grid;
}
let grid = generateGrid(ROWS, COLS);

export default {
  name: 'BigTable',
  data() {
    return {
      cols: COLS,
      rows: ROWS,
      optimized: OPTIMIZED,
      msg: 'loading...',
      grid: grid,
      dataPoints: grid.length * grid[0].items.length,
      filter: '',
    };
  },
  created() {
    window.onhashchange = function () {
      window.location.reload();
    };
    // setTimeout(() => {
    //   let s = window.performance.now();
    //   this.msg =
    //     'initial render took: ' +
    //     (window.performance.now() - s).toFixed(2) +
    //     'ms';
    // }, 0);
  },
  updated() {
    setTimeout(function () {
      let s = window.performance.now();
      console.log('aa: ', this.msg);
      this.msg =
        'initial render took: ' +
        (window.performance.now() - s).toFixed(2) +
        'ms';
    }, 0);
  },
  methods: {
    matches(item) {
      return item.value.toLowerCase().indexOf(this.filter.toLowerCase()) > -1;
    },
    visibleCount() {
      let count = 0;
      let grid = this.grid;
      for (let i = 0, l = grid.length; i < l; i++) {
        let row = grid[i].items;
        for (let j = 0, k = row.length; j < k; j++) {
          let item = row[j];
          let matched = !this.filter || matches(item);
          if (matched) {
            count++;
          }
        }
      }
      return count;
    },
    loadBase() {
      window.location.hash = '';
    },
    loadOptimized() {
      window.location.hash = '#optimized';
    },
    unmount() {
      console.profile('unmount');
      let s = window.performance.now();
      this.grid = [];
      setTimeout(() => {
        this.msg =
          'umount took: ' + (window.performance.now() - s).toFixed(2) + 'ms';
        console.profileEnd('unmount');
      }, 0);
    },
    rerender() {
      let grid = generateGrid(this.rows, this.cols);
      let s = window.performance.now();
      console.profile('rerender');
      this.grid = grid;
      setTimeout(() => {
        this.msg =
          'rerender took: ' + (window.performance.now() - s).toFixed(2) + 'ms';
        console.profileEnd('rerender');
      }, 0);
    },
  },
};
</script>

<style scoped>
@import './style.css';
</style>

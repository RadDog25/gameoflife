<script>
  import Cell from './Cell'

  export default {
    name: 'grid',
    components: {
      Cell
    },
    props: [
      'cellGrid'
    ],
    created () {

    }
  }

</script>

<template>
  <div class="grid-container layout-container">
    <div class="grid layout-row">
      <div v-for="(cellRow, cellRowIndex) in cellGrid" :key="cellRowIndex" class="row">
        <cell v-for="(cell, cellColumnIndex) in cellRow" :key="cellColumnIndex" :row="cellRowIndex"
          :column="cellColumnIndex" :cellIsAlive="cell">
        </cell>
      </div>
      <!-- end row -->
    </div>
    <!-- end grid -->
  </div>
  <!-- end grid container -->
</template>

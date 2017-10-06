<script>
  import Cell from './Cell'
  import Display from './Display'

  export default {
    name: 'grid',
    components: {
      Cell,
      Display
    },
    props: [
      'cellGrid',
      'populationIsStable',
      'generation'
    ]
  }

</script>

<template>
  <div class="grid-container layout-container">
    <div class="grid layout-row layout-layer-container">
      <display :populationIsStable="populationIsStable"
      :generation="generation"></display>
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

<script>
import Cell from './Cell'

const numberOfRows = 30
const numberOfCols = 50
const emptyCellRow = new Array(numberOfCols).fill(false)
const emptyCellGrid = new Array(numberOfRows).fill(emptyCellRow)

const portionsOfCellsAlive = 0.05
const randomCellGrid = emptyCellGrid.map(cellRow => {
  return cellRow.map(cell => {
    return Math.random() < portionsOfCellsAlive
  })
})

export default {
  name: 'grid',
  components: {
    Cell
  },
  data () {
    return {
      cellGrid: randomCellGrid
    }
  },
  methods: {
    updateCellGrid (row, column) {
      let newCellRow = this.cellGrid[row].slice()
      newCellRow[column] = true
      this.$set(this.cellGrid, row, newCellRow)
    }
  }
}
</script>

<template>
  <div class="grid-container layout-container">
    <div class="grid layout-row">
        <div v-for="(cellRow, cellRowIndex) in cellGrid"
        :key="cellRowIndex"
        class="row">
            <cell v-for="(cell, cellColumnIndex) in cellRow"
            @cellWasClicked="updateCellGrid"
            :key="cellColumnIndex"
            :row="cellRowIndex"
            :column="cellColumnIndex"
            :cellIsAlive="cell">
            </cell>
        </div><!-- end row -->
    </div><!-- end grid -->
  </div><!-- end grid container -->
</template>    


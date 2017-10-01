<script>
  import Cell from './Cell'

  const numberOfRows = 30
  const numberOfCols = 50
  const emptyCellRow = new Array(numberOfCols).fill(false)
  const emptyCellGrid = new Array(numberOfRows).fill(emptyCellRow)
  const populationDensityIncrement = 1 / (numberOfCols * numberOfRows)

  export default {
    name: 'grid',
    components: {
      Cell
    },
    props: [
      'populationDensity'
    ],
    data () {
      return {
        cellGrid: []
      }
    },
    computed: {
      currentPopulationDensity () {
        return this.cellGrid.reduce(function (totalCellsAlive, row) {
          return totalCellsAlive + row.filter(function (cellIsAlive) {
            return cellIsAlive
          }).length
        }, 0) / (numberOfRows * numberOfCols)
      }
    },
    methods: {
      updateCellGrid (row, column) {
        let newCellRow = this.cellGrid[row].slice()
        newCellRow[column] = true
        this.$set(this.cellGrid, row, newCellRow)
        this.$emit('populationDensityChanged', this.populationDensity + populationDensityIncrement)
      },
      clearGrid () {
        this.cellGrid = emptyCellGrid
      },
      getRandomCellGrid () {
        this.cellGrid = emptyCellGrid.map(cellRow => {
          return cellRow.map(cell => {
            return Math.random() < this.populationDensity
          })
        })
      },
      handlePopulationDensityChange () {
        while (this.currentPopulationDensity < this.populationDensity) {
          let randomRow = Math.floor(numberOfRows * Math.random())
          let randomColumn = Math.floor(numberOfCols * Math.random())
          console.log(randomRow, randomColumn)
          this.updateCellGrid(
            randomRow,
            randomColumn
          )
          console.log(this.currentPopulationDensity, this.populationDensity)
        }
      }
    },
    created () {
      this.getRandomCellGrid()
      window.bus.$on('clearButtonClicked', this.clearGrid)
      window.bus.$on('populationDensityChanged', this.handlePopulationDensityChange)
    }
  }

</script>

<template>
  <div class="grid-container layout-container">
    <div class="grid layout-row">
      <div v-for="(cellRow, cellRowIndex) in cellGrid" :key="cellRowIndex" class="row">
        <cell v-for="(cell, cellColumnIndex) in cellRow" @cellWasClicked="updateCellGrid" :key="cellColumnIndex" :row="cellRowIndex"
          :column="cellColumnIndex" :cellIsAlive="cell">
        </cell>
      </div>
      <!-- end row -->
    </div>
    <!-- end grid -->
  </div>
  <!-- end grid container -->
</template>

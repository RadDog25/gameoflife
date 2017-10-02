<script>
  import Grid from './components/Grid'
  import Slider from './components/Slider'
  import _ from 'underscore'

  const numberOfRows = 30
  const numberOfCols = 50
  const initialPopulationDensity = 0.05
  const initialSpeed = 0.5
  const emptyCellRow = new Array(numberOfCols).fill(false)
  const emptyCellGrid = new Array(numberOfRows).fill(emptyCellRow)
  const safetyCounter = numberOfRows * numberOfCols
  const populationDensityIncrement = 1 / (numberOfCols * numberOfRows)
  const populationDensityDifferenceThreshold = 2 * populationDensityIncrement

  function getRandomInteger (upperBound) {
    return Math.floor(upperBound * Math.random())
  }

  function arrayClone (arr) {
    if (_.isArray(arr)) {
      return _.map(arr, arrayClone)
    } else {
      return arr
    }
  }

  export default {
    name: 'app',
    components: {
      Grid,
      Slider
    },
    data () {
      return {
        cellGrid: [],
        gameIsStarted: false,
        helpIsOpen: false,
        populationDensity: 0,
        reproductionSpeed: initialSpeed
      }
    },
    methods: {
      handleClearButton () {
        this.cellGrid = emptyCellGrid
        this.populationDensity = 0
      },
      handlePopulationDensityChange (populationDensity) {
        const populationDensityDifference = Math.abs(populationDensity - this.populationDensity)
        if (populationDensityDifference > populationDensityDifferenceThreshold) {
          this.populateCellGrid(populationDensity)
        }
      },
      handleSpeedChange (speed) {
        this.reproductionSpeed = speed
      },
      handleCellClick ({row, column, cellIsAlive}) {
        let newCellRow = this.cellGrid[row].slice()
        newCellRow[column] = !cellIsAlive
        this.$set(this.cellGrid, row, newCellRow)
        this.populationDensity += cellIsAlive ? -populationDensityIncrement : populationDensityIncrement
      },
      clearGrid () {
        this.cellGrid = emptyCellGrid
      },
      populateCellGrid: _.throttle(function (targetPopulationDensity) {
        let newCellGrid = arrayClone(this.cellGrid)
        let currentPopulationDensity = this.populationDensity
        let count = 0
        let shouldIncreasePopulationDensity = targetPopulationDensity > currentPopulationDensity
        let aliveCellIndexes = []
        for (let i = 0; i < numberOfRows; i++) {
          for (let j = 0; j < numberOfCols; j++) {
            if (newCellGrid[i][j] === !shouldIncreasePopulationDensity) {
              aliveCellIndexes.push({i: i, j: j})
            }
          }
        }

        while (targetPopulationDensity > currentPopulationDensity === shouldIncreasePopulationDensity && count < safetyCounter) {
          let randomIndex = getRandomInteger(aliveCellIndexes.length)
          let randomAliveCellIndex = aliveCellIndexes[randomIndex]
          if (newCellGrid[randomAliveCellIndex.i][randomAliveCellIndex.j] === !shouldIncreasePopulationDensity) {
            newCellGrid[randomAliveCellIndex.i][randomAliveCellIndex.j] = shouldIncreasePopulationDensity
            currentPopulationDensity += shouldIncreasePopulationDensity ? populationDensityIncrement : -populationDensityIncrement
          }
          count += 1
        }
        this.cellGrid = newCellGrid
        this.populationDensity = currentPopulationDensity
      }, 500)
    },
    created () {
      this.cellGrid = emptyCellGrid
      window.bus.$on('cellWasClicked', this.handleCellClick)
    },
    mounted () {
      this.populateCellGrid(initialPopulationDensity)
    }
  }

</script>

<template>
  <div id="app">

    <div class="buttons-container layout-container">
      <div class="buttons layout-row">
        <div @click="gameIsStarted = true" class="button">
          Start
        </div>

        <div @click="gameIsStarted = false" class="button">
          Pause
        </div>

        <div @click="handleClearButton" class="button">
          Clear
        </div>

        <div @click="helpIsOpen = true" class="button">
          Help
        </div>
      </div>
    </div>
    <!-- end buttons container -->

    <grid :cellGrid="cellGrid"></grid>

    <slider @valueChanged="handleSpeedChange"
    :value="reproductionSpeed"
    >Reproduction Speed</slider>

    <slider @valueChanged="handlePopulationDensityChange"
    :value="populationDensity"
    >Population Density</slider>

  </div>
  <!-- end #app -->
</template>

<style src="./style.scss" lang="scss"></style>

<script>
  import Grid from './components/Grid'
  import Slider from './components/Slider'
  import About from './components/About'
  import _ from 'underscore'

  const numberOfRows = 30
  const numberOfCols = 50
  const initialPopulationDensity = 0.30
  const initialFrequency = 0.5
  const minPeriod = 0.15
  const maxPeriod = 0.8
  const emptyCellRow = new Array(numberOfCols).fill(false)
  const emptyCellGrid = new Array(numberOfRows).fill(emptyCellRow)
  const safetyCounter = numberOfRows * numberOfCols
  const populationDensityIncrement = 1 / (numberOfCols * numberOfRows)
  const populationDensityDifferenceThreshold = 2 * populationDensityIncrement
  let startGameId

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
      Slider,
      About
    },
    data () {
      return {
        cellGrid: [],
        gameIsStarted: false,
        aboutIsOpen: false,
        lastPopulationDensity: 0,
        populationDensity: 0,
        reproductionFrequency: initialFrequency,
        generation: 0
      }
    },
    methods: {
      handleClearButton () {
        this.cellGrid = emptyCellGrid
        this.populationDensity = 0
        this.generation = 0
        this.pauseGame()
      },
      startGame () {
        if (!this.gameIsStarted) {
          this.updateCellGrid()
          this.gameIsStarted = true
        }
      },
      pauseGame () {
        clearTimeout(startGameId)
        this.gameIsStarted = false
      },
      handleAboutCloseButtonClicked () {
        this.aboutIsOpen = false
      },
      handlePopulationDensityChange (populationDensity) {
        const populationDensityDifference = Math.abs(populationDensity - this.populationDensity)
        if (populationDensityDifference > populationDensityDifferenceThreshold) {
          this.populateCellGrid(populationDensity)
        }
      },
      updateCellGrid () {
        let newCellGrid = arrayClone(this.cellGrid)
        let neighbourCount = 0
        let populationCount = 0
        for (let i = 0; i < numberOfRows; i++) {
          for (let j = 0; j < numberOfCols; j++) {
            neighbourCount = 0
            for (let k = -1; k < 2; k++) {
              for (let l = -1; l < 2; l++) {
                if (this.cellGrid[i + k]) {
                  if (this.cellGrid[i + k][j + l] && !(k === 0 && l === 0)) {
                    neighbourCount += 1
                  }
                }
              }
            }
            if (this.cellGrid[i][j]) {
              if (neighbourCount < 2) {
                newCellGrid[i][j] = false
              } else if (neighbourCount > 3) {
                newCellGrid[i][j] = false
              }
            } else if (neighbourCount === 3) {
              newCellGrid[i][j] = true
            }
            if (newCellGrid[i][j]) {
              populationCount += 1
            }
          }
        }

        this.lastPopulationDensity = this.populationDensity
        this.generation += 1
        this.populationDensity = populationCount / (numberOfRows * numberOfCols)
        this.cellGrid = newCellGrid
        startGameId = setTimeout(this.updateCellGrid, this.reproductionPeriod * 1000)
      },
      handleFrequencyChange (frequency) {
        this.reproductionFrequency = frequency
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
    computed: {
      reproductionPeriod () {
        return (minPeriod - maxPeriod) * this.reproductionFrequency + maxPeriod
      },
      populationIsStable () {
        let lastPopulationDensityString = String(this.lastPopulationDensity).slice(0, 5)
        let populationDensityString = String(this.populationDensity).slice(0, 5)
        return lastPopulationDensityString === populationDensityString && this.populationDensity > 0
      }
    },
    created () {
      this.cellGrid = emptyCellGrid
      window.bus.$on('cellWasClicked', this.handleCellClick)
    },
    mounted () {
      this.populateCellGrid(initialPopulationDensity)
      window.bus.$on('aboutCloseButtonClicked', this.handleAboutCloseButtonClicked)
    }
  }

</script>

<template>
  <div id="app">
    <about :aboutIsOpen="aboutIsOpen"></about>
    <div class="buttons-container layout-container">
      <div class="buttons layout-row">
        <div @click="startGame" class="button">
          Start
        </div>

        <div @click="pauseGame" class="button">
          Pause
        </div>

        <div @click="handleClearButton" class="button">
          Clear
        </div>

        <div @click="aboutIsOpen = true" class="button">
          About
        </div>
      </div>
    </div>
    <!-- end buttons container -->

    <grid :cellGrid="cellGrid"
    :populationIsStable="populationIsStable"
    :generation="generation"></grid>

    <slider @valueChanged="handleFrequencyChange"
    :value="reproductionFrequency"
    >Reproduction Frequency</slider>

    <slider @valueChanged="handlePopulationDensityChange"
    :value="populationDensity"
    >Population Density</slider>

  </div>
  <!-- end #app -->
</template>

<style src="./style.scss" lang="scss"></style>

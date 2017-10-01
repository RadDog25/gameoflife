<script>
  import Grid from './components/Grid'
  import Slider from './components/Slider'

  export default {
    name: 'app',
    components: {
      Grid,
      Slider
    },
    data () {
      return {
        gameIsStarted: false,
        populationDensity: 0.05,
        reproductionSpeed: 1
      }
    },
    methods: {
      handleClearButton () {
        window.bus.$emit('clearButtonClicked')
      },
      handlePopulationDensityChange (populationDensity) {
        this.populationDensity = populationDensity
        window.bus.$emit('populationDensityChanged')
      }
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

    <grid @populationDensityChanged="handlePopulationDensityChange"
    :populationDensity="populationDensity"></grid>
    <slider :value="reproductionSpeed">Reproduction Speed</slider>
    <slider @valueChanged="handlePopulationDensityChange"
    :value="populationDensity"
    >Population Density</slider>
  </div>
  <!-- end #app -->
</template>

<style src="./style.scss" lang="scss"></style>

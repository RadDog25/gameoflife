<script>
import Grid from './components/Grid'
import Slider from './components/Slider'

export default {
  name: 'app',
  components: {
    Grid,
    Slider
  }
}
</script>

<template>
    <div id="app">
      <grid></grid>
      <slider></slider>
    </div>
</template>

<style src="./style.scss" lang="scss"></style>

<script>
import _ from 'underscore'

export default {
  name: 'slider',
  data () {
    return {
      value: '0.5',
      sliderIsBeingDragged: false
    }
  },
  computed: {
    sliderBarInnerTransform () {
      return `translateX(${this.value * 100 - 100}%)`
    }
  },
  methods: {
    handleSlider (event) {
      if (this.sliderIsBeingDragged) {
        const boundingClientRect = event.target.getBoundingClientRect()
        const value = (event.clientX - boundingClientRect.left) / boundingClientRect.width
        if (value > 1) {
          this.value = 1
        } else if (value < 0) {
          this.value = 0
        } else {
          this.value = value
        }
      }
    },
    handleMouseDown (event) {
      this.sliderIsBeingDragged = true
      this.handleSlider(event)
    },
    handleMouseUp () {
      this.sliderIsBeingDragged = false
    },
    handleMouseLeave () {
      this.sliderIsBeingDragged = false
    },
    handleMouseMove: _.throttle(function (event) {
      if (this.sliderIsBeingDragged) {
        this.handleSlider(event)
      }
    }, 100)
  }
}
</script>

<template>
  <div class="slider-container layout-container">
    <div class="slider layout-row">

      <div class="label-container">
        <div class="label">
          Label
        </div>
      </div>

      <div class="sliderbar-outer layout-layer-container"
      @mousedown="handleMouseDown"
      @mouseup="handleMouseUp"
      @mousemove="handleMouseMove"
      @mouseleave="handleMouseLeave">

        <div class="sliderbar-inner layout-layer-container"
        :style="{ transform: sliderBarInnerTransform }"></div>

        <div class="target layout-layer"></div>

      </div>

    </div><!-- end slider -->
  </div><!-- end sidebar container -->
</template>    


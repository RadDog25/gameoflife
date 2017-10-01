<script>
import _ from 'underscore'

export default {
  name: 'slider',
  props: [
    'value'
  ],
  data () {
    return {
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
        let value = (event.clientX - boundingClientRect.left) / boundingClientRect.width
        if (value > 1) {
          value = 1
        } else if (value < 0) {
          value = 0
        }
        this.$emit('valueChanged', value)
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
           <slot></slot>
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


<script>

export default {
  name: 'about',
  props: [
    'aboutIsOpen'
  ],
  methods: {
    handleClick () {
      window.bus.$emit('aboutCloseButtonClicked')
    }
  }
}

</script>

<template>
  <div v-if="aboutIsOpen" class="about-container layout-layer">
    <div class="about layout-layer-container">
      <div class="background layout-layer"></div>
      <div class="message">

        <h2 class="heading">
          About
        </h2>

        <ul>
          <li>This game is a version of <b>Conway's Game of Life.</b> You can learn more by reading <a href="https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life" target="_blank">The Wikipedia Article</a></li>
          <li>Cells can be added and removed at any time by adjusting the <b>Population Density</b> slider or by clicking on the grid</li>
          <li>The speed of the game can be changed by adjusting the <b>Reproduction Frequency</b> slider</li>
          <li>This game was built with Javascript and CSS. You can see the code at <b>Github</b></li>
        </ul>

        <a href="#" class="button">Github</a>
        <div @click="handleClick"
        class="button">Ok</div>

      </div>
    </div>
  </div>
</template>    

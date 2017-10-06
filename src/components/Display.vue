<script>
export default {
  name: 'display',
  props: [
    'populationIsStable',
    'generation'
  ]
}
</script>

<template>
  <div class="display layout-layer">
    <div class="generation">
      Generation: {{ generation }}
    </div>
    <div class="stability"
    :class="{ active: populationIsStable }">
      The population has stabalized
    </div>
  </div>
</template>    


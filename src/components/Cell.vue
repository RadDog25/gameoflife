<script>

export default {
  name: 'cell',
  props: [
    'row',
    'column',
    'cellIsAlive'
  ],
  methods: {
    handleClick () {
      this.$emit('cellWasClicked', this.row, this.column)
    }
  }
}
</script>

<template>
  <div class="cell-container"
  @click="handleClick">
    <div class="cell"
    :class="{ active: cellIsAlive }"></div>
  </div>
</template>

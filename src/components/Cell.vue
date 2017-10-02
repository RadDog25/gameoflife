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
      window.bus.$emit('cellWasClicked', {
        row: this.row,
        column: this.column,
        cellIsAlive: this.cellIsAlive
      })
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

<script>
import { Container, Draggable } from 'vue-smooth-dnd'

import Column from './Column.vue'

export default {
  name: 'Board',
  components: {
    Container,
    Draggable,
    Column
  }
}
</script>

<template>
  <container
    orientation="horizontal"
    class="board"
  >
    <draggable v-for="i in 5" :key="i">
      <column />
    </draggable>
  </container>
</template>

<style scoped>
.board {
  width: 100%;
  display: flex;
  gap: 36px;
}
</style>

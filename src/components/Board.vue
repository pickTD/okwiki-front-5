<script>
import { Container, Draggable } from 'vue-smooth-dnd'

import Column from './Column.vue'

export default {
  name: 'Board',
  components: {
    Container,
    Draggable,
    Column
  },
  data () {
    return {
      columns: [
        { title: 'To Do', id: 1 },
        { title: 'In progress', id: 2 },
        { title: 'Done', id: 3 }
      ]
    }
  },
  methods: {
    handleColumnDrop (dropResult) {
      const { removedIndex, addedIndex } = dropResult
      const updatedColumns = [
        ...this.columns.slice(0, removedIndex),
        ...this.columns.slice(removedIndex + 1)
      ]
      updatedColumns.splice(addedIndex, 0, this.columns[removedIndex])
      this.columns = updatedColumns
    }
  }
}
</script>

<template>
  <container
    orientation="horizontal"
    class="board"
    @drop="handleColumnDrop"
  >
    <draggable v-for="column in columns" :key="column.id">
      <column :title="column.title" />
    </draggable>
  </container>
</template>

<style scoped>
.board {
  width: 100%;
  padding-top: 60px;
  display: flex !important;
  justify-content: center;
  gap: 36px;
}
</style>

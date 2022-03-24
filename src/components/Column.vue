<script>
import { Container, Draggable } from 'vue-smooth-dnd'

import Card from './Card.vue'

export default {
  name: 'Column',
  components: {
    Container,
    Draggable,
    Card
  },
  props: {
    title: { type: String, required: true }
  }
}
</script>

<template>
  <div class="column">
    <h6 class="column-title">{{ title }}</h6>
    <container
      group-name="column"
      class="column-cards"
    >
      <draggable v-for="i in 5" :key="i">
        <card/>
      </draggable>
    </container>
  </div>
</template>

<style scoped>
.column {
  min-width: 300px;
  padding: 16px 8px 8px;
  background: lightblue;
  border-radius: 4px;
}

.column-title {
  margin: 0 0 12px;
  font-size: 14px;
}

.column-cards {
  display: flex;
  flex-direction: column;
  gap: 4px;
}
</style>

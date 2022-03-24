<script>
import { Container, Draggable } from 'vue-smooth-dnd'

import Card from './Card.vue'

const INITIAL_POS = 2 ** 14
const MAX_POS_BUFFER = 1024
const MIN_POS_DIVIDER = 2
const MIN_ADJACENCY = 0.1

export default {
  name: 'Column',
  components: {
    Container,
    Draggable,
    Card
  },
  props: {
    title: { type: String, required: true }
  },
  data () {
    return {
      cards: []
    }
  },
  watch: {
    cards: {
      deep: true,
      handler: function (value) {
        localStorage.setItem(this.title, JSON.stringify(value))
      }
    }
  },
  computed: {
    hasCards () {
      return !!this.cards.length
    },
    posExtremeValues () {
      const positions = this.cards.map(card => card.pos)
      return {
        min: this.hasCards ? Math.min(...positions) : null,
        max: this.hasCards ? Math.max(...positions) : null
      }
    },
    sortedCards () {
      return [...this.cards].sort((a, b) => a.pos - b.pos)
    }
  },
  methods: {
    addCard (content) {
      this.cards = [
        ...this.cards,
        {
          id: Date.now(),
          pos: this.getBottomPosition(),
          content: ''
        }
      ]
    },
    removeCard (cardId) {
      this.cards = this.cards.filter(({ id }) => id !== cardId)
    },
    updateCardContent ({ cardId, content }) {
      const card = this.cards.find(({ id }) => id === cardId)
      card.content = content
    },
    getCardPayload (index) {
      return this.sortedCards[index]
    },
    getTopPosition () {
      return this.posExtremeValues.min ? this.posExtremeValues.min / MIN_POS_DIVIDER : INITIAL_POS
    },
    getBottomPosition () {
      return this.posExtremeValues.max ? this.posExtremeValues.max + MAX_POS_BUFFER : INITIAL_POS
    },
    getMiddlePosition (index, isMovingInSingleColumn, dragDirection) {
      const prevIndex = isMovingInSingleColumn
        ? dragDirection === 'up' ? index - 1 : index
        : index - 1
      const prevPos = this.sortedCards[prevIndex].pos

      const nextIndex = isMovingInSingleColumn
        ? dragDirection === 'up' ? index : index + 1
        : index
      const nextPos = this.sortedCards[nextIndex].pos

      return (prevPos + nextPos) / 2
    },
    handleCardDrop (dropResult) {
      const { removedIndex, addedIndex, payload } = dropResult

      if ((removedIndex === null && addedIndex === null) || removedIndex === addedIndex) return

      const movedCardIndex = this.cards.findIndex(({ id }) => id === payload.id)
      let updatedCards = this.cards.slice()

      if (removedIndex !== null) {
        updatedCards.splice(movedCardIndex, 1)
      }

      if (addedIndex !== null) {
        const lastIndex = updatedCards.length - 1

        let pos

        if (addedIndex === 0) {
          pos = this.getTopPosition()
        } else if (addedIndex > lastIndex) {
          pos = this.getBottomPosition()
        } else {
          const isMovingInSingleColumn = removedIndex !== null
          const dragDirection = isMovingInSingleColumn
            ? addedIndex > removedIndex ? 'down' : 'up'
            : null
          pos = this.getMiddlePosition(addedIndex, isMovingInSingleColumn, dragDirection)
        }

        updatedCards = [...updatedCards, { ...payload, pos }]
      }
      this.cards = this.checkAdjacency(updatedCards)
    },
    checkAdjacency (cards) {
      const sortedCards = [...cards].sort((a, b) => a.pos - b.pos)
      for (let i = 1; i < sortedCards.length; i++) {
        if (sortedCards[i].pos - sortedCards[i - 1].pos < MIN_ADJACENCY) {
          return sortedCards.map((card, index) => ({
            ...card,
            pos: INITIAL_POS * (index + 1)
          }))
        }
      }
      return cards
    }
  }
}
</script>

<template>
  <div class="column">
    <h6 class="column-title">{{ title }}</h6>
    <container
      group-name="column"
      class="column-cards"
      :get-child-payload="getCardPayload"
      @drop="handleCardDrop"
    >
      <draggable v-for="card in sortedCards" :key="card.id">
        <card
          :card="card"
          @removeCard="removeCard"
          @updateCardContent="updateCardContent"
        />
      </draggable>
    </container>
    <button class="button" @click="addCard">add</button>
  </div>
</template>

<style scoped>
.column {
  width: 300px;
  padding: 16px 8px 8px;
  background: lightblue;
  border-radius: 4px;
  cursor: grab;
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

.button {
  width: 100%;
  margin-top: 12px;
}
</style>

<script>
export default {
  name: 'Card',
  props: {
    card: { type: Object, required: true }
  },
  data () {
    return {
      isEditing: false,
      content: '',
      saveTimeout: null
    }
  },
  mounted () {
    if (this.card.content) {
      this.content = this.card.content
    } else {
      this.startEditing()
    }
  },
  methods: {
    async startEditing () {
      this.isEditing = true
      await this.$nextTick()
      this.$refs.input.focus()
    },
    saveContent () {
      if (this.saveTimeout) {
        return
      }
      this.saveTimeout = setTimeout(() => {
        if (!this.content) {
          this.removeCard()
        } else if (this.content !== this.card.content) {
          this.$emit('updateCardContent', { cardId: this.card.id, content: this.content })
        }
        this.isEditing = false
        this.saveTimeout = null
      }, 100)
    },
    removeCard () {
      this.$emit('removeCard', this.card.id)
    }
  }
}
</script>

<template>
  <div class="card">
    <input
      ref="input"
      v-if="isEditing"
      v-model="content"
      type="text"
      class="card-input"
      @focusout="saveContent"
      @change="saveContent"
    >
    <div v-else class="card-content">
      <span class="card-text" @click="startEditing">{{ card.content }}</span>
      <button class="button" @click="removeCard">x</button>
    </div>
  </div>
</template>

<style scoped>
.card {
  padding: 4px 8px;
  background: beige;
  border-radius: 4px;
  cursor: grab;
}

.card-input {
  width: 100%;
  padding: 4px;
  border-radius: 2px;
  background: rgb(230, 241, 245);
}

.card-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-text {
  flex: 1;
  cursor: text;
}

.button {
  margin-left: 16px;
  flex: 0 0 24px;
}
</style>

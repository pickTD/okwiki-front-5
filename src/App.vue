<template>
  <div id="app">
    <board />
  </div>
</template>

<script>
import Board from './components/Board.vue'

export default {
  name: 'App',
  components: {
    Board
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  width: 100%;
  min-height: 100vh;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: #eff0f3;
  color: #2c3e50;
}

input {
  border: none;
  outline: none;
}

button {
  padding: 4px;
  border: none;
  outline: none;
  border-radius: 2px;
  cursor: pointer;
}

button:focus-visible {
  outline: lightblue auto 1px;
}
</style>

<template>
  <div id="app">
    <div class="container">
      <splitter />
    </div>
  </div>
</template>

<script>
import Splitter from './components/Splitter.vue'

export default {
  name: 'App',
  components: {
    Splitter
  }
}
</script>

<style lang="scss" scope>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

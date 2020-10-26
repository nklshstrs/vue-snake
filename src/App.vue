<template>
  <Grid />
</template>

<script>
  import Grid from './components/Grid.vue'

  export default {
    name: 'App',
    components: {
      Grid,
    },
    data() {
      return {}
    },
  }
</script>

<style>
  body {
    margin: 0;
  }

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    height: 100vh;
    width: 100vw;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: black;
  }
</style>

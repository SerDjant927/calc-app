<template>
  <MainContent></MainContent>
</template>

<script>
import MainContent from './components/MainContent.vue'

export default {
  name: 'App',
  components: {
    MainContent
  }
}
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

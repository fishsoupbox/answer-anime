<template>
  <div id="app">
    <capwords/>
  </div>
</template>

<script>
import capwords from './components/capwords.vue'

export default {
  name: 'App',
  components: {
    capwords
  }
}
</script>

<style lang="scss">
* {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
}
html {
    font-size: 62.5%;
    background-color: #121b1f;
}

html,body {
    height: 100%;
    width: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}


.flex {
    display: flex;
    &.center {
        justify-content: center;
        align-items: center;
    }
}
</style>

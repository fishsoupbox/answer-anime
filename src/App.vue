<template>
  <div id="app">
    <capwords :groups="groups" @complete="complete"/>
  </div>
</template>

<script>
import capwords from './components/capwords.vue'

export default {
  name: 'App',
  components: {
    capwords
  },
  data(){
    return {
        groups: [
            {
                answer: '烟瘾每次发作只会持续三分钟',
                words: ['至','烟瘾','三分','续','持','戒烟','发','次','容易','会','钟','只会','每','作','克服']
            }
        ]
    }
  },
  methods: {
    complete(sender){
        console.log(sender);
    }
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

body {
    background-image: url(./assets/capwords.gif);
    background-size: cover;
}

.flex {
    display: flex;
    &.center {
        justify-content: center;
        align-items: center;
    }
}
</style>

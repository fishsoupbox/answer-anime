<template>
  <div id="app">
    <capwords :groups="groups" @complate="complate" @complateall="complateall"/>
  </div>
</template>

<script>
import capwords from './components/capwords.vue';

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
            },
            {
                answer: '辛勤的蜜蜂永没有时间悲哀',
                words: ['的','辛勤','三分','续','永','蜜蜂','发','次','没有','会','钟','时间','每','悲哀']
            }
        ]
    }
  },
  methods: {
    /**
     * 每道题目完成时回调
     * 所有题目全部完成时回调
     */
    complate(sender){
        // index 题目下标
        // result 结果数组
        // 输出 finish 代表所有题目都已完成
        console.log(sender);
    },

    /**
     * 所有题目答题完成后，点击回调
     */
    complateall(sender){
        // index 题目下标
        // result 结果数组
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

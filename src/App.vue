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
        // 传值有两种形式, 字符串 或是 对象
        // answer   题目
        // words    选项（没有选项时，请使用方式1）
        groups: [
            // 方式2
            {
                answer: '辛勤的蜜蜂永没有时间悲哀',
                words: ['的','辛勤','永','蜜蜂','没有','时间','悲哀']
            },
            
            // 方式1
            '烟瘾每次发作只会持续三分钟',

            

            // // 方式2中word是string形式
            // {
            //     answer: '辛勤的蜜蜂永没有时间悲哀',
            //     words: '的,辛勤,永,蜜蜂,没有,时间,悲哀'
            // }
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

# animewords

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


## capwords组件配置说明

### js配置说明
```
<capwords :groups="groups" @complate="complate" @complateall="complateall"/>

groups 题目名称选项组

groups: {
    type: Array,
    default: []
}


数据格式: [String, Object]

方式一: String
groups: ['烟瘾每次发作只会持续三分钟']

方式二: Object
groups: [
	answer: {'烟瘾每次发作只会持续三分钟'},

	words数据格式分为两种,传入words会使字符串显示更灵活

	words: 

	'烟,瘾,每次,发作,只,会,持续,三分,钟',
	or
	['烟','瘾','每次','发作','只','会','持续','三分','钟'],
]


完整格式
groups: [
    // 方式1
    '烟瘾每次发作只会持续三分钟',

    // 方式2
    {
        answer: '辛勤的蜜蜂永没有时间悲哀',
        words: ['的','辛勤','永','蜜蜂','没有','时间','悲哀']
    },

    // 方式2中word是string形式
    {
        answer: '辛勤的蜜蜂永没有时间悲哀',
        words: '的,辛勤,永,蜜蜂,没有,时间,悲哀'
    }
]


回调函数

complate: 每道题目完成时回调, 所有题目全部完成时回调
参数: sender(名称自定义)

sender.index 	题目下标
sender.result 	结果数组
sender.finish 	输出 finish 代表所有题目都已完成


complateall: 所有题目答题完成后，点击回调
参数: sender(名称自定义)

sender.index 	题目下标
sender.result 	结果数组

```


### css配置说明
```
所有样式已转为em为基准
如果需要调整整体大小，修改 .capwords { font-size: 10px } 为 .capwords { font-size: 1.5rem  } ?或者其他

全局flex定义存在flex样式，如果有该定义则忽略，没有时请将flex放置在组件内部供运行
```
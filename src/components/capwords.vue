<template>
    <div class="capwords flex">
        <div class="capwords-pre"><img src="../assets/log.png" alt=""></div>
        <div class="capwords-proe flex">
            <div class="control flex center">
                <div class="procress">
                    <div class="floor"><i></i></div>
                    <div class="average" :style="{width: average}"></div>
                    <div class="complate">
                        <div class="complate-cc complate-cc1"></div>
                        <div class="complate-cc complate-cc2"></div>
                        <div class="complate-cc complate-cc3"></div>
                    </div>
                </div>
            </div>
            <div class="panel flex">
                <h1>选下方文字填补上方空白</h1>
                <div class="panel-entry flex">
                    <div class="entry-subject">
                        <div class="entry-result" :style="{height: resultHeight}">
                            <div class="entry-cate" ref="entryResult"></div>
                            <div class="entry-right iconfont icon-duigou" :class="{'entry-right-complate': state}"></div>
                        </div>
                    </div>
                    <div class="entry-subject">
                        <div class="entry-write">{{answer}}</div>
                    </div>
                    <div class="entry-group flex">
                        <div class="entry-label" :class="{'disabled': item.undisabled, 'checked': item.clickstate, 'complate': item.isanswer }" v-for="(item, index) in words" :key="index">
                            <div class="design-button" :ref="'designButton' + index" :style="{transform: 'translate('+ item.trans.x +'px, '+ item.trans.y +'px)'}" @touchstart="addState(index)" @click="addResult(index)">{{item.name}}</div>
                        </div>
                    </div>
                </div>
            </div>
            <!-- <div class="operate">
                <div class="design-button light" :class="{disable: !operate}">检查</div>
            </div> -->
        </div>
        <div class="capwords-ppop" v-if="state" @click="setOneQuest"></div>
        <audio src="../assets/999a222ae9ff4f98bed3d15bea16527b.mp3" ref="audio"></audio>
    </div>
</template>

<script>
    import TWEEN from "@tweenjs/tween.js";

    const randomsort = (a, b) => { return Math.random() > .5 ? -1 : 1; };
    let entryInty = null;

    export default {
        name: 'HelloWorld',
        props: {
            groups: {
                type: Array,
                default: []
            }
        },
        data(){
            return {
                qesIndex: 0,        // 当前题目下标
                allGroupNum: 1,     // 题目总个数
                entry: {},
                calcsize: [],

                // 实际输入项
                answer: "我喜欢你手机中的小猫",
                words: ['欢','你','小猫','喜','我','中','的','手机'],

                result: [],         // 每次选择时存储的字符组
                resultIdx: [],      // 每次选择时存储的下标组

                averagenum: 0,      // 进度条当前提目下标动画参数
                state: false,       // 每个题目完成时的状态判断

                entryX: 0,
                entryY: 0,
                line: 0
            }
        },
        computed: {
            entryResult(){
                return this.$refs.entryResult;
            },
            operate(){
                return this.result.length;
            },
            average(){
                return parseInt(this.averagenum / this.allGroupNum * 100) + '%'
            },
            resultHeight(){
                return this.entry.height + this.entryY + 'px'
            }
        },
        watch:{
            groups:{
                handler(newV, oldV){
                    this.allGroupNum = newV.length;
                    this.setOneQuest();
                },
                immediate: true
            }
        },
        methods: {
            /**
             * 下一个题目
             */
            setOneQuest(){
                const qesIndex = this.qesIndex;

                const initstate = this.initPage();

                if(initstate){
                    this.words = this.words.map((item, index)=>{
                        item.clickstate = false;
                        item.undisabled = false;
                        item.isanswer = false;
                        item.trans = {x: 0, y: 0};
                        return item;
                    })
                    this.$emit('complateall', {
                        index: qesIndex,
                        result: this.result
                    });
                    return;
                }

                let group = this.groups[qesIndex], answer = group, words = null;

                // 判断group每一项是否为string
                if(typeof(group) === 'string'){
                    const _words = answer.split("");
                    words = _words.sort(randomsort);
                }else{
                    answer = group.answer;
                    words = group.words;
                    if(typeof(words) === 'string'){
                        words = words.split(",");
                    }
                }

                
                words = words.map((item, index)=>{
                    const word = {};
                    word.name = item;
                    word.clickstate = false;
                    word.undisabled = false;
                    word.isanswer = false;
                    word.trans = {x: 0, y: 0};
                    return word;
                })

                this.words = words;
                this.answer = answer;
            },
            /**
             * 重置题目条件
             */
            initPage(){
                this.calcsize = [];
                this.result = [];
                this.entryX = 0;
                this.entryY = 0;
                this.line = 0;

                this.state = false;
                if(this.qesIndex > 0){
                    setTimeout(()=>{
                        const entryResult = this.entryResult.getBoundingClientRect();
                        entryResult.height = entryInty.height;
                        this.entry = entryResult;
                    }, 600)
                }


                // 完成所有答题时
                if(this.qesIndex > this.allGroupNum - 1){
                    return true;
                }
                return false;
            },
            /**
             * 完成题目
             */
            complate(){
                const qesIndex = this.qesIndex + 1;
                this.qesIndex = qesIndex;

                
                // 滚动条效果
                const size = {
                    average: this.averagenum
                }
                const tween = new TWEEN.Tween(size).to({ average: qesIndex }, 300).easing(TWEEN.Easing.Quadratic.Out).onUpdate(()=>{
                    this.averagenum = size.average;
                }).start();

                // 播放音频
                this.$refs.audio.play();
                

                // 完成所有答题时
                if(qesIndex > this.allGroupNum - 1){
                    this.$emit('complate', {
                        index: qesIndex,
                        result: this.result,
                        finish: true
                    });
                    return;
                }

                this.$emit('complate', {
                    index: qesIndex,
                    result: this.result
                });
            },
            /**
             * 判断输入框中按钮
             */
            checkWords(index){
                const word = this.words[index];
                // 如果点击的按钮是输入框中的或者按钮被禁用的情况，阻止
                if(word.undisabled || word.isanswer){
                    return true;
                }
                const answer = this.answer.substring(this.result.join('').length);

                if(new RegExp('^' + word.name).test(answer)){
                    this.result.push(word.name);
                    word.isanswer = true;
                }else{
                    if(!new RegExp(word.name).test(answer)){
                        word.undisabled = true;
                    }
                    word.clickstate = false;
                    this.words[index] = word;

                    return true;
                }

                word.clickstate = false;

                this.words[index] = word;
                
                return false;
            },

            /**
             * 输入框中添加按钮
             */
            addResult(index){
                if(this.checkWords(index)){
                    return;
                }

                // 获取当前按钮
                const designButton = this.$refs['designButton' + index][0].getBoundingClientRect();
                // 获取输入框
                const entry = this.entry;

                // 计算差值
                let x = entry.x - designButton.x;
                let y = entry.y - designButton.y;
                
                if(this.result.length){
                    const lastResult = this.calcsize[this.result.length - 1];
                    x = x + this.entryX;
                }

                //如果超出当前行则换行
                if(this.entryX + designButton.width > entry.width){
                    x = entry.x - designButton.x;
                    this.line = this.line + 1;
                    this.entryY = this.entryY + designButton.height;
                    this.entryX = 0;

                    this.words = this.words.map((item)=>{
                        if(item.isanswer){
                            item.trans.y = item.trans.y - designButton.height;
                        }
                        return item;
                    })

                }
                this.entryX = this.entryX + designButton.width;

                y = y - this.entryY + designButton.height * this.line;

                this.words[index].trans = {x, y};

                this.calcsize.push(designButton);
                this.resultIdx.push(index);

                if(this.answer === this.result.join("")){
                    this.state = true;
                    this.complate();
                }
            },
            addState(index){
                const word = this.words[index];
                const answer = this.answer.substring(this.result.join('').length);
                if(!new RegExp('^' + word.name).test(answer)){
                    word.clickstate = true;
                }

                this.words[index] = word;
            },
            tweenAnimate(){
                TWEEN.update();
                requestAnimationFrame(this.tweenAnimate);
            }
        },
        mounted(){
            const entryResult = this.entryResult.getBoundingClientRect();
            this.entry = entryInty = entryResult;
            this.averagenum = this.qesIndex;
            this.tweenAnimate();
        }
    }
</script>

<style scoped lang="scss">
    @import url(../assets/font/iconfont.css);
    .capwords {
        max-width: 750px;
        padding: 1.2em;
        max-height: 80vh;
        width: 100%;
        bottom: 0;
        position: absolute;
        left: 50%;
        background-color: #ffffff;
        border-radius: 10px;
        transform: translateX(-50%);
        font-size: 10px;
        .capwords-pre {
            position: absolute;
            height: 8em;
            width: 8em;
            padding: .5em;
            border-radius: 50%;
            background-color: #ffffff;
            left: 50%;
            margin-left: -4em;
            top: -4em;
            img {
                width: 100%;
            }
        }
        .capwords-proe {
            flex-direction: column;
            justify-content: space-between;
            background-color: #f1f1f1;
            border-radius: 1em;
            padding: 1.2em;
            flex: 1;
        }
        .capwords-ppop {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            z-index: 5;
        }
    }

    .control {
        padding: 0 1.8em;
        margin-top: 3em;
        .procress {
            position: relative;
            height: 1.8em;
            flex: 1 0 auto;
            border: 2px solid #ffffff;
            border-radius: 50px;
            & div {
                border-radius: 50px;
            }
            .floor {
                background-color: #ffd8be;
                box-shadow: 0px 0px 5px rgba(0,0,0,.3) inset;
                height: 100%;
                i {
                    position: absolute;
                    background-image: linear-gradient(90deg, rgba(255, 221, 199, .5) 35%,
                        transparent 35%,
                        transparent 50%,
                        rgba(255, 221, 199, .5) 50%,
                        rgba(255, 221, 199, .5) 85%,
                        transparent 85%,
                        transparent);
                        height: 40%;
                        width: 96%;
                        top: 30%;
                        left: 2%;
                        background-size: 16px 10px;
                }
            }
            .average {
                background-color: #ec3656;
                box-shadow: 0px 0px 5px rgba(0,0,0,.3) inset;
                height: 100%;
                padding: 0.4em 0.6em;
                position: absolute;
                top: 0;
                min-width: 1%;
                max-width: 100%;
                width: 50%;
            }
        }
    }
    .panel {
        padding: 2em 0;
        flex-direction: column;
        h1 {
            color: #000;
            font-size: 1.8em;
            font-weight: 400;
            margin-bottom: 1.8em;
            font-weight: 700;
            text-align: center;
        }
        .panel-entry {
            flex: 1 0 auto;
            justify-content: center;
            flex-direction: column;
            .entry-subject {
                margin-bottom: 1.4em;
            }
            .entry-result {
                box-shadow: 0px 2px 3px 1px rgba(70, 47, 5, .1);
                background-color: #ffffff;
                border-radius: 6px;
                height: 3.8em;
                padding-right: 4em;
                position: relative;
            }
            .entry-cate {
                height: 100%;
                width: 100%;
            }
            .entry-right {
                position: absolute;
                right: 1em;
                top: 50%;
                transform: translateY(-50%);
                color: #28e879;
                opacity: 0;
                transition: opacity .1s;
                &.entry-right-complate {
                    opacity: 1;
                    transition: opacity .2s;
                }
            }
            .entry-write {
                font-size: 1.4em;
                color: #000000;
                text-align: center;
                font-weight: 700;
            }
            .entry-group {
                flex-wrap: wrap;
                justify-content: center;
            }
            .entry-label {
                box-shadow: 0px 2px 3px 1px rgba(70, 47, 5, .1);
                background-color: #d6d6d6;
                border-radius: 6px;
                margin: .7em;
                .design-button {
                    padding: 0.5em 1.0em;
                    transition: all .2s;
                }
                &:active {
                    box-shadow: 0px 1px 1px 0px rgba(70,47,5,.1);
                }
                &.disabled {
                    box-shadow: 0 0 0 rgba(0,0,0,0);
                    .design-button {
                        transition: all .3s;
                        background-color: #af2525;
                        color: #eeeeee;
                    }
                }
                &.checked:hover, &.checked:checked {
                    .design-button {
                        transition: none;
                        background-color: #af2525;
                        color: #eeeeee;
                    }
                }
                &.complate:hover, &.complate:active {
                    box-shadow: 0px 2px 3px 1px rgba(70, 47, 5, .1);
                    .design-button {
                        background-color: #ffffff;
                        color: #000000;
                    }
                }
                
            }
        }
    }

    .operate {
        margin-bottom: 2vh;
        height: 4.2rem;
        .design-button {
            font-weight: 700;
            position: relative;
            z-index: 1;
        }
    }
    .design-button {
        border-radius: 6px;
        background-color: #ffffff;
        color: #000000;
        font-size: 1.6rem;
        text-align: center;
        padding: .8rem;
        transition: transform .3s;
        &:active {
            
        }
        &.disable {
            background-color: #313d44;
            color: #44565b;
        }

        &.light {
            background-color: #8ddf34;
            color: #121b1f;
            transition: none;
        }
        &.light:active {
            transform: translateY(2px);
        }
    }
</style>

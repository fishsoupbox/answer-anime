<template>
    <div class="capwords flex">
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
                        <div class="entry-result" ref="entryResult">
                            <div class="entry-cate"></div>
                        </div>
                    </div>
                    <div class="entry-subject">
                        <div class="entry-write">{{answer}}</div>
                    </div>
                    <div class="entry-group flex">
                        <div class="entry-label" :class="{'disabled': item.undisabled, 'checked': item.undisabled }" v-for="(item, index) in words" :key="index">
                            <div class="design-button" :ref="'designButton' + index" @click="addResult(index)">{{item.name}}</div>
                        </div>
                    </div>

                </div>
            </div>
            <!-- <div class="operate">
                <div class="design-button light" :class="{disable: !operate}">检查</div>
            </div> -->
        </div>
    </div>
</template>

<script>
    let entryX = 0, entryY = 0;

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
                // 当前题目下标
                qesIndex: 0,
                // 题目总个数
                allGroupNum: 1,
                // 计算属性
                entry: {},
                calcsize: [],
                // 实际输入项
                answer: "我喜欢你手机中的小猫",
                words: ['电脑','欢','你','小猫','喜','我','小狗', '中','的','手机'],
                result: [],
                resultIdx: []
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
                return parseInt(this.qesIndex / this.allGroupNum * 100) + '%'
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
                let answer = this.groups[qesIndex].answer;
                let words = this.groups[qesIndex].words;

                this.initPage();

                if(typeof(words) === 'string'){
                    words = words.split(",");
                }
                words = words.map((item, index)=>{
                    const word = {};
                    word.name = item;
                    word.clickstate = false;
                    word.undisabled = false;
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
                entryX = 0;
                entryY = 0;
            },
            /**
             * 完成题目
             */
            complate(){
                const qesIndex = this.qesIndex + 1;

                this.$emit('complate');
                
                // 答题完成
                if(qesIndex > this.allGroupNum - 1){
                    this.$emit('complateall');
                    return;
                }

                this.qesIndex = qesIndex;
            },
            /**
             * 判断输入框中按钮
             */
            checkWords(index){
                const word = this.words[index];
                // 如果点击的按钮是输入框中的或者按钮被禁用的情况，阻止
                if(word.clickstate && word.undisabled){
                    return true;
                }
                const answer = this.answer.substring(this.result.join('').length);

                if(new RegExp('^' + word.name).test(answer)){
                    console.log(word.name);
                    this.result.push(word.name);
                }else{
                    if(!new RegExp(word.name).test(answer)){
                        word.undisabled = true;
                    }
                    word.clickstate = true;
                    return true;
                }

                this.words = words;
                
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
                    x = x + entryX;
                }

                //如果超出当前行则换行
                if(entryX + designButton.width > entry.width){
                    x = entry.x - designButton.x;
                    entryY = entryY + designButton.height;
                    entryX = 0;
                }
                entryX = entryX + designButton.width;

                y = y + entryY;

                this.$refs['designButton' + index][0].style.transform = "translate("+x+"px, "+y+"px)";

                this.calcsize.push(designButton);
                this.resultIdx.push(index);

                if(this.answer === this.result.join("")){
                    debugger;
                    this.complate();
                }
            }
        },
        mounted(){
            const entryResult = this.entryResult.getBoundingClientRect();
            this.entry = entryResult;
        }
    }
</script>

<style scoped lang="scss">
    @import url(../assets/font/iconfont.css);
    .capwords {
        max-width: 750px;
        padding: 1.2rem;
        max-height: 80vh;
        overflow: hidden;
        width: 100%;
        bottom: 0;
        position: absolute;
        left: 50%;
        background-color: #ffffff;
        border-radius: 1rem;
        transform: translateX(-50%);
        .capwords-proe {
            flex-direction: column;
            justify-content: space-between;
            background-color: #f1f1f1;
            border-radius: 1rem;
            padding: 1.2rem;
        }
    }


    .control {
        padding: 0 1.8rem;
        margin-top: 3rem;
        .procress {
            position: relative;
            height: 1.8rem;
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
                padding: 0.4rem 0.6rem;
                position: absolute;
                top: 0;
                min-width: 1%;
                max-width: 100%;
                width: 50%;
            }
        }
    }
    .panel {
        padding: 2rem 0;
        flex-direction: column;
        h1 {
            color: #000;
            font-size: 1.8rem;
            font-weight: 400;
            margin-bottom: 1.8rem;
            font-weight: 700;
            text-align: center;
        }
        .panel-entry {
            flex: 1 0 auto;
            justify-content: center;
            flex-direction: column;
            .entry-subject {
                margin-bottom: 1.4rem;
            }
            .entry-result {
                
            }
            .entry-cate {
                box-shadow: 0px 2px 3px 1px rgba(70, 47, 5, .1);
                background-color: #ffffff;
                border-radius: 6px;
                height: 3.8rem;
            }
            .entry-write {
                font-size: 1.4rem;
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
                margin: .7rem;
                .design-button {
                    padding: 0.8rem 1.4rem;
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

<template>
    <div class="capwords flex">
        <div class="control flex center">
            <div class="close iconfont icon-guanbi1"></div>
            <div class="procress">
                <div class="floor"></div>
                <div class="average"></div>
                <div class="complate">
                    <div class="complate-cc complate-cc1"></div>
                    <div class="complate-cc complate-cc2"></div>
                    <div class="complate-cc complate-cc3"></div>
                </div>
            </div>
            <div class="life flex center">
                <div class="life-ico">
                    <div class="iconfont icon-aixin_shixin"></div>
                </div>
                <div class="health">{{health}}</div>
            </div>
        </div>
        <div class="panel flex">
            <h1>翻译这句话</h1>
            <div class="panel-entry flex">
                <div class="entry-subject">
                    <div class="entry-cartoon">
                        <div class="cartoon">
                            
                        </div>
                        <div class="dialog">
                            <p class="dialog-tiney flex">
                                <i class="iconfont icon-xiaolaba"></i>
                                <span v-for="(word, index) in words" :key="index">{{word}}</span>.
                            </p>
                        </div>
                    </div>
                    <div class="entry-result" ref="entryResult">
                        <div class="entry-cate"></div>
                        <div class="entry-cate"></div>
                    </div>
                </div>
                <div class="entry-group flex center">
                    <div class="entry-label" v-for="(item, index) in tselect" :key="item">
                        <div class="design-button" :ref="'designButton' + index" @click="addResult(index)">{{item}}</div>
                    </div>
                </div>

            </div>
        </div>
        <div class="operate">
            <div class="design-button light" :class="{disable: !operate}">检查</div>
            <div class="falyer flex">
                <div class="falyer-result">{{falyer}}!</div>
                <div class="falyer-flag"><span class="iconfont icon-qi"></span></div>
            </div>
        </div>
    </div>
</template>

<script>
    let entryX = 0, entryY = 0, line = 0;
    export default {
        name: 'HelloWorld',
        props: {
            msg: String
        },
        data(){
            return {
                health: 5,
                calcsize: {
                    entry: {},
                    res: []
                },
                words: ['I', 'like', 'your', 'computer'],
                tselect: ['电脑','的','手机','你','小猫','喜欢','我','小狗', '中文'],
                result: [],
                falyerResult: ['正确', '错误']
            }
        },
        computed: {
            entryResult(){
                return this.$refs.entryResult;
            },
            operate(){
                return this.result.length;
            },
            falyer(){
                return this.falyerResult[0]
            }
        },
        methods: {
            addResult(index){
                const designButton = this.$refs['designButton' + index][0].getBoundingClientRect();
                const entry = this.calcsize.entry,
                      entryPadding = entry.mypadding.split(" ").map((item)=>{
                        return parseInt(item);
                      });

                let x = entry.x - designButton.x;
                let y = entry.y - designButton.y;
                
                x = x + entryPadding[0]
                y = y + entryPadding[1];

                if(this.result.length){
                    const lastResult = this.calcsize.res[this.result.length - 1];
                    x = x + entryX;
                }
                if(entryX + designButton.width > entry.width + entryPadding[0]){
                    entryY = entryY + designButton.height + entry.mydist;
                    x = entry.x - designButton.x + entryPadding[0];
                    entryX = 0;
                    line += 1;
                }
                entryX = entryX + entryPadding[0] + designButton.width;

                y = y + entryY;

                this.$refs['designButton' + index][0].style.transform = "translate("+x+"px, "+y+"px)";


                this.calcsize.res.push(designButton);
                this.result.push(index);
            }
        },
        mounted(){
            const entryResult = this.entryResult.getBoundingClientRect();
            // 内边距
            entryResult.mypadding = "6 7";
            // 内边距
            entryResult.mydist = 17;
            this.calcsize.entry = entryResult;
            console.log(entryResult);
        }
    }
</script>

<style scoped lang="scss">
    @import url(../assets/font/iconfont.css);
    .capwords {
        max-width: 750px; margin: 0 auto;
        padding: 1.6rem;
        height: 100vh;
        overflow: hidden;
        justify-content: space-between;
        flex-direction: column;
    }

    .control {
        .close {
            color: #475a5f;
            font-size: 2.6rem;
        }
        .procress {
            position: relative;
            height: 1.5rem;
            flex: 1 0 auto;
            padding: 0 2rem;
            & div {
                border-radius: 50px;
            }
            .floor {
                background-color: #313d44;
                height: 100%;
            }
            .average {
                background-color: #8fdd34;
                height: 100%;
                padding: 0.4rem 0.6rem;
                position: absolute;
                top: 0;
                min-width: 1%;
                max-width: 100%;
                &:after {
                    content: "";
                    background-color: #ffffff;
                    opacity: 0.18;
                    height: 60%;
                    width: 100%;
                    position: relative;
                    display: block;
                    border-radius: 50px;
                }
            }
        }
        .life {
            color: #ed2741;
            .life-ico {
                margin-right: .8rem;
                position: relative;
                .iconfont {
                        filter: drop-shadow(0px 0px 2px rgba(232, 43, 62, 0.5));
                    font-size: 2.6rem;
                }
                &:after {
                    content: "";
                    position: absolute;
                    width: .6rem;
                    height: .6rem;
                    border-radius: 50%;
                    background-color: #f3596e;
                    left: 16%;
                    top: 23%;
                }
            }
            .health {
                font-size: 1.4rem;
            }
            
        }
    }
    .panel {
        padding: 1.2rem 0;
        max-height: calc(100vh - 6rem);
        flex: 1 0 auto;
        flex-direction: column;
        h1 {
            color: #ffffff;
            font-size: 2.4rem;
            font-weight: 400;
            margin-bottom: 4vh;
            letter-spacing: .1rem;
        }
        .panel-entry {
            flex: 1 0 auto;
            justify-content: center;
            flex-direction: column;
            .entry-subject {
                margin-bottom: 10vh;
            }
            .entry-cartoon {
                position: relative;
                height: 11.6rem;
                .cartoon {
                    position: absolute;
                    background-color: rgba(12, 130, 182, 1);
                    height: 100%;
                    width: 10em;
                    left: 10vw;
                }
                .dialog {
                    position: absolute;
                    padding: 1.4rem;
                    padding-bottom: .7rem;
                    border: 2px solid #343e46;
                    border-radius: 10px;
                    background-color: #121b1f;
                    color: #ffffff;
                    font-size: 1.6rem;

                    width: 16.5rem;
                    left: calc(10vw + 7em);
                    top: -2rem;

                    &:after {
                        content: "";
                        position: absolute;
                        background-color: #121b1f;
                        height: 1.4rem;
                        width: 1.4rem;
                        border: 2px solid #343e46;
                        border-right-color: #121b1f;
                        border-radius: 40px 0px 0px 0px;
                        right: 100%;
                        bottom: 1.5rem;
                    }

                    .dialog-tiney {
                        flex-wrap: wrap;
                        align-items: center;
                    }
                    .iconfont {
                        color: #25b0f1;
                        font-size: 2.2rem;
                        margin-right: .4rem;
                        margin-bottom: 0.7rem;
                    }
                    span {
                        padding: 2px;
                        border-bottom: 2px dotted #535c60;
                        margin: 0 4px;
                        margin-bottom: 0.7rem;
                        text-align: center;
                        min-width: 1.8rem;
                        &:last-child {
                            margin-right: 0;
                        }
                    }
                }
            }
            .entry-result {
                
            }
            .entry-cate {
                border-bottom: 2px solid #343d45;
                height: 5.4rem;
                &:first-child {
                    border-top: 2px solid #343d45;
                }
            }
            .entry-group {
                flex-wrap: wrap;
                padding: 0 1rem;
            }
            .entry-label {
                background-color: #313d44;
                border-radius: 12px;
                margin: .3rem .2rem;
                .design-button {
                    padding: .6rem 1.2rem;
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
        .falyer {
            background-color: #1f2a2f;
            position: absolute;
            width: 100%;
            padding: 1.6rem;
            padding-bottom: calc(4.2rem + 2vh + 3.6rem);
            left: 0;
            bottom: calc(-4.2rem - 2vh - 1.6rem - 5rem);
            bottom: 0;
            align-items: center;
            justify-content: space-between;


            color: #8ddf34;
            font-size: 2rem;
            font-weight: 700;
            
        }
        .falyer-result {
            font-size: 2rem;
        }
        .falyer-flag {
        }
    }
    .design-button {
        border-radius: 12px;
        background-color: #121b1f;
        border: 2px solid #313d44;
        color: #ffffff;
        font-size: 1.6rem;
        text-align: center;
        border-bottom-width: 4px;
        padding: .8rem;
        transition: transform .3s;
        &:active {
            border-bottom-width: 2px;
        }
        &.disable {
            background-color: #313d44;
            border-bottom-width: 2px;
            color: #44565b;
        }

        &.light {
            background-color: #8ddf34;
            border-width: 0 0 5px 0;
            border-color: #73c031;
            color: #121b1f;
            transition: none;
        }
        &.light:active {
            border-bottom-width: 3px;
            transform: translateY(2px);
        }
    }
</style>

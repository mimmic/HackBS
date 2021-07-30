<template>
<div class="Hoga-chart-wrapper">
    <div class="Hoga-main-chart">
        <div class="hoga-tool-bar-wrapper">
            <top-tool-bar
            :content="tools"
            />
        </div>
        <div class="hogaBar-container" v-for="(i, index) in Array(10)"
        :key="`offer-${index}`"
        >
            <div class="hogaBar hogaBar-right"  :style="offerBarStyle(0, index)">{{OfferText[0][index]}}</div>
            <div class="hogaBar hogaBar-right"  :style="offerBarStyle(1, index)">{{OfferText[1][index]}}</div>
            <div class="hogaBar hogaBar-center" :style="offerBarStyle(2, index)">{{OfferText[2][index]}}</div>
            <div class="hogaBar"></div>
            <div class="hogaBar"></div>
        </div>
        <div class="hogaBar-container" v-for="(i, index) in Array(10)"
        :key="`bid-${index}`"
        >
            <div class="hogaBar"
            ></div>
            <div class="hogaBar"
            ></div>
            <div class="hogaBar hogaBar-center" :style="bidBarStyle(2, index)">{{BidText[2][index]}}</div>
            <div class="hogaBar" :style="bidBarStyle(3, index)">{{BidText[1][index]}}</div>
            <div class="hogaBar" :style="bidBarStyle(4, index)">{{BidText[0][index]}}</div>
        </div>
        <div class="hogaBar-container hogaBar-last-section">
            <div class="hogaBar last-hogaBar hogaBar-right">{{totor}}</div>
            <div class="hogaBar last-hogaBar"></div>
            <div class="hogaBar hogaBar-center last-hogaBar">{{curTime}}</div>
            <div class="hogaBar last-hogaBar"></div>
            <div class="hogaBar last-hogaBar">{{totbr}}</div>
        </div>
    </div>
    <div class="Hoga-chart-footer">
        <div class="hogaBar-container Hoga-footer-wrapper">
            <div class="footer-button">
                <div class="hoga-replay-button"
                @click="clickButton('left')"
                >
                    &lt;
                </div>
            </div>
            <div class="footer-button"></div>
            <div class="footer-button">
                <div class="hoga-replay-button"
                @click="clickButton('play')"
                :style="PlayButtonStyle"
                >
                    {{PlayButtonText}}
                </div>
            </div>
            <div class="footer-button"></div>
            <div class="footer-button">
                <div class="hoga-replay-button"
                @click="clickButton('right')"
                >
                    &gt;
                </div>
            </div>
        </div>
    </div>
</div>    
</template>

<script>
import TopToolBar from './topTool/TopToolBar.vue'
// rgb(248, 73, 96); offer
// rgb(2, 192, 118); bid

const OCPACITY = .25;
export default {
    components: { TopToolBar },
    props : ['groupData', 'openPrice'],
    data() {
        return {
            tools : [{'text' : "호가창"}, {is : "Spacer"}],
            OfferText : this.hInit(),
            BidText : this.hInit(),
            curTime : "",
            totor : "",
            totbr : "",
            IsPlay : false,
            PlayButtonText : "Play",
            timerId : null
        }
    },
    mounted () {
        this.init()
    },
    methods : {
        hInit() {
            let Arr = []
            for(let i=0; i < 10 ; i++){ Arr.push(["", "", ""])}
            return Arr
        },
        comma(x) {
            return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")
        },
        marks(x){
            if (x > 0) return '+' + String(x)
            return String(x)
        },
        init(){
            this.OfferText = this.hInit()
            this.BidText = this.hInit()
            this.curTime = ""
            this.totor = ""
            this.totbr = ""
        },
        playStart(){
            if (this.IsPlay) {
                this.$emit("eventBus",{
                    event : "hoga-replay",
                    args : 1
                })
                this.timerId = setTimeout(()=>{
                    this.playStart()
                }, 150)
            }
        },
        playStop(){
            this.IsPlay = false
            this.PlayButtonText = "Play"
            clearTimeout(this.timerId)
        },
        clickButton(e){
            switch(e){
                case 'left':
                    if (this.IsPlay){ this.playStop()}
                    this.$emit("eventBus",{
                        event : "hoga-replay",
                        args : -1
                    })
                    break;
                case 'right':
                    if (this.IsPlay){ this.playStop()}
                    this.$emit("eventBus",{
                        event : "hoga-replay",
                        args : 1
                    })
                    break;
                case 'play':
                    if (this.IsPlay){
                        this.playStop()
                    } else {
                        this.IsPlay = true
                        this.PlayButtonText = "Stop"

                        this.$emit("eventBus",{
                            event : "hoga-replay",
                            args : 1
                        })
                        this.timerId = setTimeout(()=> {
                            this.playStart()
                        }, 150)
                    }
                    break;
            }
        }
    },
    computed : {
        offerBarStyle() {
            return (idx1, idx2) => {
                if ((idx1 !== 0)){
                    return {
                        'background-color' : 'rgb(2, 192, 118, 0.2)',
                    }
                }
                if (!!!this.$props.groupData) { return null }
                
                let per = Math.round(this.$props.groupData.or[idx2] / this.$props.groupData.mr * 100)
                return {
                    'background' : `linear-gradient(to left, rgb(2, 192, 118, 0.2) ${per}%, transparent 0)`,
                    'border-right-color' : 'rgb(2, 192, 118, 0.2)'
                }
            }
        },
        bidBarStyle() {
            return (idx1, idx2) => {
                if((idx1 !== 4)){
                    return {
                        'background-color' : 'rgb(248, 73, 96, 0.2)',
                    }
                }
                if (!!!this.$props.groupData) { return null }
                let per = Math.round(this.$props.groupData.br[idx2] / this.$props.groupData.mr * 100)
                return {
                    'background' : `linear-gradient(to right, rgb(248, 73, 96, 0.2) ${per}%, transparent 0)`,
                    'border-left-color' : 'rgb(248, 73, 96, 0.2)'
                }
            }
        },
        PlayButtonStyle(){
            if (this.IsPlay) return { 'background-color' : '#131722'}
            return { 'background-color' : null }
        }
    },
    
    watch : {
        'groupData' : function() {
            // :@TODO hoga data format 다시짜기
            let gd = this.$props.groupData
            let openP = this.$props.openPrice
            this.curTime = gd.time
            this.totor = gd.totor
            this.totbr = gd.totbr
            for(let i=0; i < 10 ; i++){
                if (gd.or[i] !== 0){
                    this.OfferText[0][i] = this.comma(gd.or[i])
                } else {
                    this.OfferText[0][i] = ""
                }
                if (gd.br[i] !== 0) {
                    this.BidText[0][i] = this.comma(gd.br[i])
                } else {
                    this.BidText[0][i] = ""
                }

                this.OfferText[1][i] = `${this.marks(((gd.op[i] / openP - 1) * 100).toFixed(2))}%`
                this.OfferText[2][i] = this.comma(gd.op[i])

                this.BidText[1][i] = `${this.marks(((gd.bp[i] / openP - 1) * 100).toFixed(2))}%`
                this.BidText[2][i] = this.comma(gd.bp[i])

            }
        }
    }
    
}
</script>

<style>
.Hoga-chart-wrapper{
    display: flex;
    flex-wrap: wrap;
    height: 100%;
    width : 100%;
}
.hoga-tool-bar-wrapper {
    font-size: 14px;
    margin-bottom: 13px;
    padding: 10px 16px 4px;
    border-bottom: 1px solid #d1d4dc;
}
.hogaBar-container{
    display: flex;
    flex-wrap: nowrap;
    width: 100%;
}
.hogaBar {
    flex-basis: 0;
    flex-grow: 1;
    height : 15px;
    border : 1px solid rgb(0, 0, 0, 0.1);
    color : rgb(255, 255, 255, .75);
    font-size : 11px;
}
.hogaBar-left{
    text-align: left;
}
.hogaBar-right{
    text-align: right;
}
.hogaBar-center{
    text-align: center;
}
.last-hogaBar{
    background-color: rgb(24, 26, 32);
}
.Hoga-main-chart{
    display: flex;
    flex-direction: column;
    width : 100%;
    height : 404px;
}
.Hoga-chart-footer{
    flex-grow: 1;
    flex-basis: 0;
    position: relative;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    white-space: nowrap;
    width: 100%;
    height: 45px;
}
.Hoga-footer-wrapper{
    height : 100%;
}
.footer-button {
    flex-basis: 0;
    flex-grow: 1;
    height : 100%;
    align-items: center;
    position: relative;
    display: table;
    text-align: center;
}
.hoga-replay-button{
    color : rgb(255, 255, 255, .5);
    font-size : 13px;
    height : 100%;
    width : 40%;
    display: table-cell;
    vertical-align: middle;
    border-radius: 6px;
}
.hoga-replay-button:hover{
    background-color: #131722;
}

</style>
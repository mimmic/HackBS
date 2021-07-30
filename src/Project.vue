<template>
<div>
    <div class='Project-container container '>
        <div class="Main-container container">
            <main-chart
            :hname="hname"
            :chart="chart"
            :timeframe="timeframe"
            @eventBus="eventBus"
            >
            </main-chart>
        </div>
        <div class="Sub-container container">
            <div class="bs-container container">    
                <buy-sell-view
                :groupData="onChart"
                />
            </div>
            <div class="Hoga-container container">
                <hoga-chart
                :groupData="hogaList"
                :openPrice="openPrice"
                @eventBus="eventBus"
                >
                </hoga-chart>
            </div>
        </div>
        <div class="wl-container container">
            <watch-list-view
            :groupData="watchList"
            :date="date"
            :selectedItem="code"
            @eventBus="eventBus"
            />
            
        </div>
    </div>    
</div>
</template>

<script>
import MainChart from './component/MainChart.vue'
import HogaChart from './component/HogaChart.vue'
import BuySellView from './component/BuySellView.vue'
import WatchListView from './component/WatchListView.vue'
import DataCube from './tradingVue/helpers/datacube.js'
import NAME_DIC from './data/NAME_DIC.json'

import DATA from './data/sample.json'

export default {
  components: { MainChart, HogaChart, BuySellView, WatchListView},
    name : "app",
    mounted () {
        this.loadData()
        window.dc = this.chart
        window.tv = this.$refs.tradingVue
    },
    methods : {
        comma(x) {
            return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")
        },
        eventBus (d) {
            switch(d.event){
                case "file-load":
                    this.data = d.args.data
                    if (this.date === d.args.fname.split('.')[0]){
                        return
                    }
                    this.date = d.args.fname.split('.')[0]
                    this.loadData()
                    break;
                case "item-selected": 
                    if (this.code === d.args) { return }
                    this.code = d.args
                    if (this.selectedCodes.includes(this.code)){
                        this.reAssign()
                    } else {
                        this.assignData()
                    }
                    break;
                case "time-select-change":
                    // ON-CHART HORIZENTAL LINE UPDATE
                    this.$set(this.data[this.code]['data']["onchart"][1], 'data', [
                        [d.args, this.openPrice]
                    ])
                    break;
                case "hoga-select-button":
                    let s = d.args + ":00"
                    let IsFind = false
                    // :@TODO segment tree search
                    for(let i = 0; i < this.hogaData.length ; i++){
                        if (s > this.hogaData[i].time){ continue }
                        IsFind = true
                        this.hogaSelectedIdx = i
                        break;
                    }
                    if (!IsFind) { this.hogaSelectedIdx = this.hogaData.length - 1}
                    this.hogaList = this.hogaData[this.hogaSelectedIdx]
                    break;
                case "hoga-replay":
                    if((d.args < 0) && (this.hogaSelectedIdx === 0)){
                        break;
                    } 
                    else if (this.hogaSelectedIdx === this.hogaData.length - 1){
                        break;
                    }
                    this.hogaSelectedIdx += d.args
                    this.hogaList = this.hogaData[this.hogaSelectedIdx]
                    break;
            }
        },
        hogaRefine(){
            if (!!!this.data[this.code]['hoga']) {return}
            this.hogaData = this.data[this.code]['hoga'].map(x => { return {
                time : x[0],
                totor : x[1],
                totbr : x[2],
                mr : x[3],
                or : x.slice(4,14),
                op : x.slice(14,24),
                br : x.slice(24,34),
                bp : x.slice(34,44)
            }})
            this.hogaList = this.hogaData[this.hogaSelectedIdx]
        },
        reAssign(){
            let chart_data = this.data[this.code]["data"]
            this.hname = this.NAME_DIC[this.code]
            this.openPrice = chart_data['chart']['data'][0][1]
            this.timeframe = chart_data['chart']['data'].map((x, i)=>x[0])

            this.chart = new DataCube(chart_data)

            this._BS_ASSIGN(chart_data.onchart[0].data)
        },
        assignData(){
            this.selectedCodes.push(this.code)
            this.hname = this.NAME_DIC[this.code]
            this.openPrice = this.data[this.code]["data"]["ohlcv"][0][1]
            let chart_data = this.data[this.code]["data"]
            this.onChart   = chart_data['onchart'][0]["data"]
            chart_data['onchart'][0].settings = {"z-index": 5}
            
            // 호가 리플레이 스크롤 연동
            this.timeframe = this.data[this.code]["data"]["ohlcv"].map((x, i)=>x[0])

            chart_data['onchart'].push({
                name : "hLine",
                type : "hLine",
                data : [[this.data[this.code]["data"]["ohlcv"][0][0], this.openPrice]],
                settings : {"z-index" : 5}
            })
            
            this.chart = new DataCube(chart_data)

            this._BS_ASSIGN(chart_data['onchart'][0]["data"])
            this.hogaRefine()

        },
        loadData () {
            let data = this.data
            this.code = Object.keys(data)[0]
            
            this.hname = this.NAME_DIC[this.code]
            // :@TODO 데이터 가공 다시 만들기 
            this.watchList = Object.keys(data).map(
                x=> {
                    return {
                        code : x,
                        hname : NAME_DIC[x],
                        len : data[x]["data"]['onchart'][0]["data"].reduce((acc, cur, i)=> {
                        return acc + cur[2].length }, 0)
                    }
                }
            )
            this.assignData()
        },
        _BS_ASSIGN(onChartData) {
            let _onChart = []
            for(let i=0; i < onChartData.length; i++){
                for(let j=0; j < onChartData[i][3].length; j++){
                    _onChart.push({
                        time : onChartData[i][4].split(' ')[1],
                        bs : onChartData[i][2][j],
                        price : onChartData[i][1],
                        vol : onChartData[i][3][j],
                        val : this.comma(onChartData[i][3][j] * onChartData[i][1])
                    })
                }
            }
            this.onChart = _onChart
        }
    },
    data () {
        return {
            data : DATA,
            chart : new DataCube({}),
            hname : "",
            code : "",
            NAME_DIC : NAME_DIC,
            onChart : [],
            watchList : [],
            date : "sample",
            openPrice : 0,
            hogaData : [],
            hogaList : null,
            hogaSelectedIdx : 0,

            timeframe : [],
            selectedCodes : [],
        }
    }
}
</script>

<style scoped>
body {
    margin: 0;
    padding: 0;
    overflow: hidden;
    font-family: -apple-system,BlinkMacSystemFont,
    Segoe UI,Roboto,Oxygen,Ubuntu,Cantarell,
    Fira Sans,Droid Sans,Helvetica Neue,
    sans-serif;
    background-color: rgb(24, 26, 32);/*#434651;*/
}
.container {
    display: flex;
    background-color : rgba(43, 47, 54, 0.5);
}
.Project-container{
    position: relative;  
    color : #fff;
    background-color: #1e222d;
    height : 100vh;
    grid-gap: 10px;
    display: flex;
    flex: 1 1 auto;
}
.Main-container{
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    height: 100%;
}
.Hoga-container{
    /* flex-grow: 2; */
    display: flex;
    width: 100%;
    height: 449px;
}
.Sub-container{
    flex-grow: 5;
    display: flex;
    flex-direction: column;
    height: 100%;
}
.bs-container{
    flex-grow: 1;
    height: 40%;
    border-bottom : 10px solid #1e222d;
}
.wl-container{
    flex-grow: 1;
    width: 10%;
    height: 100%;
}
</style>

<template>
<div>
    <div class="trading-vue-container">
        <trading-vue :data="chart" :width="this.width" :height="this.height"
        :toolbar="false"
        :titleTxt="hname"
        :overlays="overlays"
        :index-based="index_based"
        :color-back="colors.colorBack"
        :color-grid="colors.colorGrid"
        :color-text="colors.colorText"
        >
        </trading-vue>
    </div>
    <div class="trading-vue-footer">
        <div class="tv-footer-left-flag"></div>
        <div class="tv-footer-scroll">
            <vue-slider v-model="tfIdx"
            :data="tfArray"
            @change="sliderChange"
            v-bind="options"
            />
        </div>
        <div class="tv-footer-right-flag"
        @click="onHogaButtionClick"
        >
            호가창 &gt;
        </div>
    </div>
</div>
</template>

<script>
import TradingVue from '../tradingVue/TradingVue.vue'
import BS_Marker from './Overlays/BS_Marker.vue'
import hLine from './Overlays/hLine.vue'
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/default.css'

export default {
    name : "MainChart",
    props : ["chart", "hname", "timeframe"],
    components : { 
        TradingVue, VueSlider
    },
    mounted () {
        window.addEventListener('resize', this.onResize)
        this.onResize()
        window.dc = this.chart
        window.tv = this.$refs.tradingVue
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.onResize)
    },
    methods: {
        onResize(e) {
            this.width = window.innerWidth * .59;
            this.height = window.innerHeight * .95;
        },
        init_options(){
            return {
                max: (this.$props.timeframe.length || 380) - 1,
                tooltip: 'active',
            }
        },
        sliderChange(){
            this.$emit('eventBus', {
                event : 'time-select-change',
                args : this.tfDic[this.tfIdx],
                tests : this.tfIdx
            })
        },
        onHogaButtionClick(){
            this.$emit('eventBus', {
                event : 'hoga-select-button',
                args : this.tfIdx
            })
        },
        load_tfDic() {
            const timeframe = this.$props.timeframe
            this.tfDic = {}
            for (let i=0 ; i < timeframe.length ; i++){
                let d = new Date(timeframe[i])
                let h = d.getHours()
                h = h > 9 ? h - 9 : h + 15
                this.tfDic[`${String(h).padStart(2, '0')}:${String(d.getMinutes()).padStart(2, '0')}`] = timeframe[i]
            }
            this.tfArray = Object.keys(this.tfDic)
        }
    },
    computed: {
        colors() {
            return {
                colorBack: 'rgba(30, 32, 38, 0.498)',//'#fff',
                colorGrid: '#333', //'#eee',
                colorText: '#fff', //'#333'
            }
        },
    },
    data () {
        return {
            width : window.innerWidth * .59,
            height : window.innerHeight * .95,
            tv_width : window.innerWidth * .59,
            index_based: true,
            overlays : [BS_Marker, hLine],
            tfIdx : 0,
            options : this.init_options(),
            tfDic : {},
            tfArray : ['09:00'],
        }
    },
    watch : {
        'timeframe' : function() {
            this.tfIdx = 0
            this.options = this.init_options()
            this.load_tfDic()
        }
    }
}
</script>

<style>
.test-border {
    border : 1px solid orange;
}
.trading-vue-container{
    position: relative;
    display: flex;
    width : 100%;
    height : 98%;
    background-color : rgb(30, 32, 38);
}
.trading-vue-footer{
    display: flex;
    flex-wrap: wrap;
    width: 100%;
    background-color : rgb(30, 32, 38);
}
.tv-footer-left-flag{
    display: block;
    width : 15px;
    min-height: 28px;
    height: 100%;
}
.tv-footer-scroll{
    display: block;
    flex-grow: 1;
}
.tv-footer-right-flag{
    bottom : 10px;
    margin-left: 15px;
    color: #787b86;
    background-color: #2a2e39;
    border-color: #363a45;
    margin-right: 10px;
    display: inline-block;
    position: relative;
    height: 32px;
    max-width: 250px;
    flex-shrink: 0;
    white-space: nowrap;
    padding: 8px 12px 0;
    box-sizing: border-box;
    border: 1px solid;
    border-radius: 4px;
    font-weight: 700;
    font-size: 12px;
    overflow: hidden;
    text-overflow: ellipsis;
    cursor: pointer;
    vertical-align: middle;
    transition: background-color .175s ease,opacity .175s ease;
}
.tv-footer-right-flag:hover{
    background-color : #131722;
}
</style>
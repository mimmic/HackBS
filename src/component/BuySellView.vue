<template>
<div class='BS-wrapper'>
<sub-contain-form :tools="tools">
    <slot>
        <div class="body-content-container">
            <perfect-scrollbar ref="BS-scroll">
                <buy-sell-list v-for="(item, index) in groupData"
                :item="item"
                :key="`${index}`"
                />
            </perfect-scrollbar>
        </div>
    </slot>
</sub-contain-form>
</div>
</template>

<script>
import SubContainForm from './SubContainForm.vue'
import { PerfectScrollbar } from "vue2-perfect-scrollbar";
import "vue2-perfect-scrollbar/dist/vue2-perfect-scrollbar.css";
import BuySellList from './iterableList/BuySellList.vue';

export default {
    name:"BuySellView",
    components: {SubContainForm, PerfectScrollbar, BuySellList},
    props : {
        groupData : {
            type : Array,
            default : []
        },
    },
    methods : {

    },
    data () {
        return {
            tools : [ {text : '매매내역'}]
        }
    },

}
</script>

<style>
.BS-wrapper{
    height : 100%;
    width : 100%;
}
.body-content-container{
    position: relative;
    cursor: default;
    overflow: hidden;
    height: 100%;
}

.ps{
    height : 100%;
}
.ps .ps__rail-y.ps--clicking {
  background-color: transparent;
  opacity: 0.3;
}
.ps .ps__rail-x:hover, .ps .ps__rail-y:hover, .ps .ps__rail-x:focus, .ps .ps__rail-y:focus, .ps .ps__rail-x.ps--clicking, .ps .ps__rail-y.ps--clicking {
    background-color: transparent;
    opacity: 0.9;
}
.ps__rail-y {
    width: 3px;
}
.ps__thumb-y {
    opacity: 0;
}
.ps__thumb-x {
    opacity: 0;
}
</style>
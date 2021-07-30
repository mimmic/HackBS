<template>
<div class="sub-form-wrapper">
    <div class="tool-bar-wrapper">
        <top-tool-bar
        v-if="IsTools"
        :content="tools"
        />
    </div>
    
    <div class="body-content-wrapper"
    :style="{height : HeightStyle}"
    >
        <slot/>
    </div>
</div>
</template>

<script>
import TopToolBar from './topTool/TopToolBar.vue'
export default {
    components : {TopToolBar},
    props : {
        tools : {
            type : Array,
            default : []
        },
        IsHeightMax : {
            type : Boolean,
            default : false
        }
    },
    computed : {
        IsTools () {
            return this.$props.tools.length !== 0 
        },
        HeightStyle () {
            return this.$props.IsHeightMax ? "95%" : null
        }
    }
}
</script>

<style
TopToolBar>
.sub-form-wrapper{
    display: flex;
    flex-wrap: wrap;
    height: 100%;
    width: 100%;
}
.tool-bar-wrapper{
    width : 100%;
    height : 30px;
    font-size: 14px;
    margin: 0;
    padding: 10px 16px 4px;
    border-bottom : 1px solid #d1d4dc;
}
.body-content-wrapper {
    height: 90%;
    width: 100%;
    padding : 0;
}
</style>
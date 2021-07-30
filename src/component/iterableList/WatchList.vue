<template>
<div class="watch-list-container"
@click="onClickEvent"
:style="Style"
>
    <div class="watch-list-header">
        <div class="watch-list-header-text">
            {{item.hname}}
        </div>
    </div>
    <div class="watch-list-space">
    </div>
    <div class="watch-list-numCode">
        {{item.len}}
    </div>
</div>
</template>

<script>
export default {
    props : ['item', 'selectedItem'],
    computed : {
        Style () {
            if (this.$props.selectedItem === this.$props.item.code) {
                return {
                    'background-color' : "#21384d",
                    'border-left' : "3px solid #2962ff"
                }
            }
            return {}
        }
    },
    methods : {
        onClickEvent (e) {
            e.preventDefault()
            this.$emit("onEvent", this.$props.item)
        }
    }
}
</script>

<style>
.watch-list-container{
    color: #d1d4dc;
    position: relative;
    padding: 15px 0 15px 10px;
    border-bottom: 1px solid #4f5966;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    white-space: nowrap;
    width: 100%;
}
.watch-list-container:hover {
    background-color : rgb(24, 26, 32);/*#2a2e39;*/
}
.watch-list-header-text{
    display: flex;
    flex: 0 1 100%;
    justify-content: space-between; 
    box-sizing: border-box;
    min-width: 0;
    max-width: 100%;
    padding-bottom: 5px;
    height: 15px;
    line-height: 15px;
    vertical-align: middle;
}
.watch-list-space{
    flex-grow: 1;
}
.watch-list-numCode{
    margin-right: 15px;
    position: relative;
    align-items: center;
    white-space: nowrap;
    text-align: left;
}
</style>
<template>
<div class="buy-sell-list-container">
    <div class="bs-contain-wrapper">
        <span class="bs-signal-image_"
            :style="{
                'background-color' : bsStyle,
                'border-color' : bsStyle
            }"
        >{{item.bs}}</span>
        <div class="bs-time-text">
            {{item.time}}
        </div>
        <div class="bs-price-text">
            {{priceText}}
        </div>
        <div class="bs-left-text">
            <div class="bs-vol-text">
                {{item.vol}}(주)
            </div>
            <span>{{item.val}}</span>
        </div>
    </div>
</div>
</template>

<script>
export default {
    props : ['item'],
    computed : {
        priceText() {
            return String(this.$props.item.price) + "    "
        },
        bsStyle() {
            return this.$props.item.bs === "S" ? "rgb(41, 98, 255)" : null
        }
    }
}
</script>

<style>
.bs-contain-wrapper{
    position: relative;
    padding: 15px 15px 5px;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    white-space: nowrap;
}
.bs-signal-image_{
    background-color: #d32f2f;
    border-color: #d32f2f;
    color: #fff;
    border: none;
    border-radius: 7px;
    text-align: center;
    vertical-align: middle;
    margin: 0;
    text-align: center;
    box-sizing: border-box;
    padding: 1% 2%;
    position: relative;
    cursor: pointer;
    text-align: center;
    transition: background-color .35s ease,border-color .35s ease,color .35s ease;
    font-size : 12px
}
.bs-time-text{
    padding-left : 2%;
}
.bs-price-text{
    padding-left : 10%;
}
.bs-left-text{
    font-size : 11px;
    margin-left: auto;
}

._margin-right{
    padding-right : 25px;
}

.buy-sell-text{
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
.buy-sell-list-container{
    color: #d1d4dc;
    position: relative;
    border-bottom: 1px solid #4f5966;
    display: flex;
    width: 100%;
    flex-direction: column;
}
.buy-sell-header{
    position: relative;
    padding: 15px 15px 5px;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    white-space: nowrap;
}
.buy-sell-body{
    position: relative;
    padding: 0 15px 10px;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    white-space: nowrap;
    font-size : 11px
}
.buy-sell-left{
    position: relative;
    align-items: center;
    white-space: nowrap;
    text-align: left; 
}
.buy-sell-list-container:hover {
    background-color : rgb(24, 26, 32);
}
.bs-signal-image{
    background-color: #d32f2f;
    border-color: #d32f2f;
    color: #fff;
    border: none;
    border-radius: 4px;
    text-align: center;
    vertical-align: middle;
    margin: 0;
    text-align: center;
    box-sizing: border-box;
    padding: 1% 10%;
    right: 1px;
    position: absolute;
    cursor: pointer;
    text-align: center;
    transition: background-color .35s ease,border-color .35s ease,color .35s ease;
    font-size : 12px
}


</style>
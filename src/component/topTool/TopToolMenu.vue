<template>
<div class="top-tool-menu">
    <!--<div class="extended-hover-zone"></div>-->
    <div class="top-tool-menu-items" :style="{
        width: width+'px',
        minWidth: width+'px',
        maxHeight: height+'px',
        overflow: height ? 'auto' : 'visible',
    }">
        <component v-for="(item, index) in menu"
        :is="get_component(item.is)"
        :item="item"
        :class="item.class"
        :key="'menu-'+index"
        :index="index"
        />
    </div>
</div>
</template>

<script>
import TopToolMenuItem from './TopToolMenuItem.vue'
import TopToolMenuSeparator from './TopToolMenuSeparator.vue'

export default {
    components: { TopToolMenuItem, TopToolMenuSeparator },
    props: {
        menu: {
            type: Array,
            required:true
        },
        width: Number,
        height: Number,
    },
    methods: {
        get_component(is) {
            if(typeof is == "object") return is;
            else if(typeof is == "string") return 'top-tool-menu-'+is;
            return 'top-tool-menu-item';
        }
    }
}
</script>

<style>
.bar-menu-items {
    position: absolute;
    min-width: 160px;
    color: #787b86;
    background: 'rgba(30, 32, 38, 0.498)';
    padding: 5px 0;
    box-shadow: 0 2px 4px rgb(0 0 0 / 20%);/*rgba(60, 64, 67, 0.15) 0px 2px 6px 2px;*/
}
</style>
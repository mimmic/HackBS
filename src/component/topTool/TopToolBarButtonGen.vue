<template>
    <div class="top-tool-button" :class="button_class" v-on="$listeners"
    @mousedown="(e) => e.preventDefault()"
    @mouseover="onMouseUp"
    @mouseleave="onMouseLeave"
    @click="(e) => (item.click && !item.disabled) ? item.click(e) : e.stopPropagation()"
    >
    <span v-if="item.icon" class="material-icons icon" 
    :style="{ color :textColor, 'background-color' : textBackColor}"
    >{{ item.icon }}</span>
    <span v-if="item.html" class="top-tool-button-html-label"
    :style="{ color :textColor, 'background-color' : textBackColor}" v-html="item.html"></span>
    <span v-else-if="item.text"  class="top-tool-button-label"
    :style="{ color :textColor, 'background-color' : textBackColor}">{{ item.text }}</span>
    
    <component class="menu" v-if="item.menu"
        :is="get_component(item.menu)"
        :menu="item.menu"
        :class="item.menu_class"
        :id="item.menu_id"
        :width="item.menu_width"
        :height="item.menu_height"
        :style="{
            left : item.menu_left + '%'
        }"
         />
    </div>
</template>

<script>
import hotkey_manager from './imports/hotkey-manager.js'
import TopToolMenu from './TopToolMenu.vue'

export default {
    name : "TopToolBarButtonGen",
    components: {
        TopToolMenu
    },

    mixins: [hotkey_manager],
    props: {
        item: {
            type: Object,
            required: true
        },
        is_open: Boolean,
        selected: Boolean,
    },
    methods: {
        get_component (is) {
            if(is && !Array.isArray(is) && typeof is == "object") return is;
            else return "top-tool-menu";
        },
        onMouseUp(e){
            this.textColor = "rgb(240, 185, 11)"
            this.textBackColor = "#2a2e39"
        },
        onMouseLeave(e) {
            this.textColor = null
            this.textBackColor = null
        },
    },
    computed: {
        is_menu () { return this.item.menu ? true : false; },
        button_class() {
            const open = this.is_open && this.is_menu && this.$props.selected;
            const active = this.item.active;
            const disabled = this.item.disabled;
            return {open, active, disabled}
        },
    },
    mounted() {},
    beforeDestroy() {},
    data() {
        return {
            textColor : null,
            textBackColor : null,
        };
    },
}
</script>

<style>
.top-tool-button {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    /* padding : 7px; */
    padding: 0 5px;
    border-radius: 3px;
    white-space: nowrap;
}


.top-tool-button > .menu {
    position: absolute;
    left: 0;
    top: 100%;
    z-index: 1006;
    display: None;
}

.top-tool-button.open > .menu {
    position: absolute;
    left: 0%;
    top: 100%;
    display: block;
    z-index: 1006;
    background-color: #1e222d;

}
.top-tool-button-html-label {
    align-items: center;
    justify-content: center;
    padding : 0;
    margin : 0;
}
.top-tool-button-label {
    font-size : 14px
}
</style>
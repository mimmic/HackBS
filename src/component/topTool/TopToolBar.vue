<template>
<div class='top-tool-bar'>
    <component v-for="(item, item_idx) in content"
        :is="get_component(item.is)"
        :key="'top-tool-bar-'+item_idx"
        :ref="'top-tool-bar-'+item_idx"
        :item="item"
        :class="item.class"
        :id="item.id"
        :is_open="menu_open"
        :selected="is_selected(item_idx)"
        @click="(event) => toggle_menu('top-tool-bar-'+item_idx, item_idx, event)"/>
</div>
</template>

<script>
import TopToolBarButtonGen from "./TopToolBarButtonGen.vue"
import TopToolSpacer from "./TopToolSpacer.vue"
import TopToolSeparator from "./TopToolSparator.vue"

export default {
    name : "TopToolBar",
    components: {
        TopToolBarButtonGen, TopToolSpacer, TopToolSeparator
    },

    props: {
        content: {
        type: Array,
        required: true
        },
    },
    methods: {
        get_component(is){
            if(is && !Array.isArray(is) && typeof is == "object") return is;
            else if(typeof is == "string") return "top-tool-"+is;
            return "top-tool-bar-Button-gen"
        },
        clickaway (e) {
            if(!this.$el.contains(e.target)) this.menu_open = false;
        },
        toggle_menu(name, idx, event){
            event.stopPropagation();
            const ref = this.$refs[name][0];
            const disabled = ref.item && ref.item.disabled;
            const touch = event.sourceCapabilities && event.sourceCapabilities.firesTouchEvents;
            this.menu_open = ref.is_menu && !disabled ? (touch ? true : !this.menu_open) : false;
            this.selected_idx = idx
        },
        is_selected(idx){
            return idx === this.selected_idx
        }
    },
    computed: {

    },
    mounted() {
        document.addEventListener("click", this.clickaway);
    },
    beforeDestroy() {
        document.removeEventListener("click", this.clickaway);
    },
    data() {
        return {
            menu_open: false,
            selected_idx: -1
        }
    },
}
</script>

<style>
.top-tool-bar{
    display: flex;
    align-items: stretch;
    justify-content: flex-start;
    flex-wrap: wrap;
    /* background-color : #131722; */
    /* border: 1px solid #b2b5be; */
}
</style>
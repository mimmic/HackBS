<template>
    <div class="top-tool-menu-item"
        @mousedown="(e) => e.preventDefault()"
        @click="click"
        :class="{ disabled: item.disabled, active: item.active }"
        :style="{ height: item.height+'px' }"
    >
    <span v-if="item.text" class="label">{{item.text}}</span>
    <span v-if="item.hotkey" class="hotkey">{{ hotkey }}</span>
    </div>
</template>

<script>
import hotkey_manager from './imports/hotkey-manager.js'
export default {
    mixins: [ hotkey_manager ],
    props: {
        item: {
            type: Object,
            required: true
        },
        index : Number,
    },
    methods: {
        click(e) {
            if(this.item.click && !this.item.disabled) { 
                this.item.click(e);
            }
            else if (!this.$refs.menu || !e.composedPath || !e.composedPath().includes(this.$refs.menu.$el)) {
                e.stopPropagation(); // prevent menu close for touch devices
            }
        }
    }
}
</script>

<style>
.top-tool-menu-item{
    position: relative;
    display: flex;
    align-items: center;
    justify-content: flex-start;
    padding: 8px 15px;
}
.top-tool-menu-item:hover {
    background-color: #2a2e39/*#76878319; */
}
</style>
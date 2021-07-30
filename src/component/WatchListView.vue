<template>
<div class="wl-container-wrapper">

<sub-contain-form 
:tools="tools"
:IsHeightMax="true">
    <slot>
        <div class="wl-body-content-container">
            <perfect-scrollbar ref="WatchList-scroll">
                <watch-list v-for="(item, index) in groupData"
                :item="item"
                :selectedItem="selectedItem"
                :key="`${index}`"
                @onEvent="onEvent"
                />
            </perfect-scrollbar>-->
        </div>
    </slot>
</sub-contain-form>
<input type="file" ref="file_input" @change='onFileUpload' style="display:none"/>
</div>
</template>

<script>
import SubContainForm from './SubContainForm.vue'
import { PerfectScrollbar } from "vue2-perfect-scrollbar";
import "vue2-perfect-scrollbar/dist/vue2-perfect-scrollbar.css";
import WatchList from './iterableList/WatchList.vue';

export default {
    name : "WatchListView",
    components : {SubContainForm, PerfectScrollbar, WatchList},
    props : ['groupData', 'date', 'selectedItem'],
    methods : {
        onFileUpload(e) {
            let reader = new FileReader()
            let file = e.target.files[0]
            reader.onload = (event) => {
                this.$emit('eventBus', {
                    event : 'file-load',
                    args : {
                        data : JSON.parse(new TextDecoder("utf-8").decode(event.target.result)),
                        fname : file.name
                    }
                })
            }
            reader.readAsArrayBuffer(file)
        },
        onEvent(d) {
            console.log(d)
            this.$emit('eventBus', {
                event : 'item-selected',
                args : d.code
            })
        },
        init_tool() {
            return [ {
                html : `<span>${this.$props.date}    </span><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 14" width="16" height="14" class="tv-screener-toolbar__button-icon tv-screener-toolbar__button-icon--export"><g fill="none" stroke="currentColor" stroke-width="1.5"><path d="M15 9v4H1V9M8 9V0"></path><path d="M5 6l3 3 3-3"></path></g></svg>`,
                click : () => {
                    this.$refs.file_input.click()
                }
            }]
        }
    },
    data () {
        return  {
            tools : this.init_tool()
        }
    },
    watch : {
        date() {
            this.tools = this.init_tool()
        }
    }
}
</script>

<style>
.wl-container-wrapper{
    display : flex;
    width : 100%;
    height : 100%;
}

.wl-body-content-container{
    position: relative;
    cursor: default;
    overflow: hidden;
    height: 100%;
}

</style>
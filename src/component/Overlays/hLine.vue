<script>
import Overlay from '../../../src/tradingVue/mixins/overlay.js'

export default {
    name: "hLine",
    mixins: [Overlay],
    methods : {
        meta_info() {
            return  { author: 'NA', version: '1.0.0' }
        },
        draw(ctx) {
            let layout = this.$props.layout
            this.$props.data.forEach((p, i) => {
                ctx.beginPath()
                let x = layout.t2screen(p[0])
                let y = layout.$2screen(p[1])
                
                ctx.strokeStyle = "#d9ed28"
                ctx.lineWidth = 1
                ctx.moveTo(x, this.layout.height)
                ctx.lineTo(x, 0)
                ctx.setLineDash([2,2])
                ctx.stroke()
            })
        },
        use_for() { return ['hLine'] },

    },
    data () {
        return {
        }
    }
}
</script>

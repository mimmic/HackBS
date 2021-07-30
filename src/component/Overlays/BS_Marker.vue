<script>

import Overlay from '../../../src/tradingVue/mixins/overlay.js'
import Hint from './Hint.js'

export default {
    name : "BS_Marker",
    mixins: [Overlay],
    methods : {
        meta_info() {
            return  { author: 'NA_', version: '1.0.0' }
        },
        draw(ctx) {
            let layout = this.$props.layout
            this.IsInHover = false
            this.$props.data.forEach((p, i) => {
                ctx.beginPath()
                let x1 = layout.t2screen(p[0])
                let y  = layout.$2screen(p[1])
                let bs = p[2]
                let is_hover = this.hover(x1, y)

                let _color = "#ed112b"
                if (bs.includes("B") && bs.includes("S")){
                    _color = "#44c667"
                }
                else if (bs.includes("B")) {}
                else { 
                    _color =  "#fff"
                }

                ctx.strokeStyle = _color
                ctx.arc(x1, y, 4, 0, Math.PI * 2, true)
                ctx.fillStyle =_color 
                ctx.fill()

                if (is_hover) {
                    ctx.beginPath()
                    let vol_str = `가격 : ${p[1]}\n`
                    let nl = '\n'
                    for(let i = 0 ; i < bs.length; i++){
                        if (i == bs.length - 1) {nl = ''}
                        vol_str += `${p[3][i]} (${p[2][i]}) - ${this.comma(p[1] * p[3][i])}원${nl}`
                    }

                    let y$ = p[1]
                    this.hint = new Hint(this, {
                        t: p[0],
                        y$: y$,
                        w: 150,
                        h: 65 + 21 * (bs.length - 1),
                        text: `시간 : ${p[4]}\n` + vol_str
                    })
                    this.hint.draw(ctx)
                }
            })
        },

        use_for() { return ['BS_Marker'] },
        mousemove(event) {
            this.mouse.x = event.layerX
            this.mouse.y = event.layerY
        },
        hover(x1, y) {
            return ((x1 - this.mouse.x) * (x1 - this.mouse.x) + (
            y - this.mouse.y ) * (y - this.mouse.y ) < 16
            )
        },
        comma(x) {
            return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")
        }
    },

    computed : {
        new_font() {
            return '16px ' + this.$props.font.split('px').pop()
        },
    },
    data () {
        return {
            mouse: { x: undefined, y: undefined },
            selected: null,
            IsInHover: false,
        }
    }
}
</script>

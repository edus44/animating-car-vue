<template>
    <svg>
        <use 
            :xlink:href="carHref"
            :width="width"
            :height="height" 
            :x="originX" 
            :y="originY"
            :transform="transform"
        ></use>
    </svg>
</template>

<script>

import TWEEN from './tween.js'

export default {
    data:()=>({
        carHref: require('./car.svg')+'#Capa_1',
        width:50,
        height:50,
        x:100,
        y:100,
        angle:45
    }),
    computed:{
        originX(){
            return this.x - this.width/2
        },
        originY(){
            return this.y - this.height/2
        },
        transform(){
            return `rotate(${this.angle} ${this.x} ${this.y})`
        }
    },
    created(){
        let pressed = {}
        window.addEventListener('keydown',e=>{
            if (pressed[e.keyCode]) return
            else pressed[e.keyCode] = 1

            if (e.keyCode == 37) this.turnStart(-1)
            else if (e.keyCode == 39) this.turnStart(1)
        })
        window.addEventListener('keyup',e=>{
            delete pressed[e.keyCode]

            if (e.keyCode == 37) this.turnStop(-1)
            else if (e.keyCode == 39) this.turnStop(1)
        })

        this.turn = {
            speed: 0,
            tween: null
        }

        this.render()
    },
    methods:{
        
        turnStart(dir){
            if (this.turn.tween) this.turn.tween.stop()

            this.turn.tween = new TWEEN.Tween(this.turn)
                .to({speed: 1 * dir},500)
                .easing(TWEEN.Easing.Quadratic.In)
                .chain(
                    new TWEEN.Tween(this.turn)
                    .repeat(Infinity)
                )
                .start()
        },

        turnStop(dir){
            if (this.turn.tween) this.turn.tween.stop()

            this.turn.tween = new TWEEN.Tween(this.turn)
                .to({speed: 0},300)
                .easing(TWEEN.Easing.Quadratic.Out)
                .start()
        },

        render(){
            requestAnimationFrame(this.render.bind(this))
            TWEEN.update()

            this.angle += this.turn.speed * 3
        }
    }

}
</script>

<style>
html,body,svg{
    width: 100%;
    height: 100%;
    display: block;
    margin:0;
    padding: 0;
}
</style>
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

            // Block successive events of the same key
            if (pressed[e.keyCode]) return
            else pressed[e.keyCode] = 1

            if (e.keyCode == 37) this.start(-1,'turn')       // Left
            else if (e.keyCode == 39) this.start(1,'turn')   // Right
            else if (e.keyCode == 38) this.start(1,'move')   // Up
            else if (e.keyCode == 40) this.start(-1,'move')  // Down
        })

        window.addEventListener('keyup',e=>{
            delete pressed[e.keyCode]

            if (e.keyCode == 37) this.stop('turn')
            else if (e.keyCode == 39) this.stop('turn')
            else if (e.keyCode == 38) this.stop('move')
            else if (e.keyCode == 40) this.stop('move')
        })

        this.turn = {
            speed: 0,
            tween: null
        }
        this.move = {
            speed: 0,
            tween: null
        }

        this.render()
    },
    methods:{

        // Starts speeding tween
        start(dir,type){
            if (this[type].tween) this[type].tween.stop()

            this[type].tween = new TWEEN.Tween(this[type])
                .to({speed: dir},500)
                .easing(TWEEN.Easing.Quadratic.In)
                .chain(
                    new TWEEN.Tween(this[type])
                    .repeat(Infinity)
                )
                .start()
        },

        // Slow the speed down to 0
        stop(type){
            if (this[type].tween) this[type].tween.stop()

            this[type].tween = new TWEEN.Tween(this[type])
                .to({speed: 0},300)
                .easing(TWEEN.Easing.Quadratic.Out)
                .start()
        },

        // Update car values
        render(){

            // Recursive call
            requestAnimationFrame(this.render.bind(this))

            // Update tween values
            TWEEN.update()

            // Update angle based on turn speed
            let backwards = this.move.speed >= 0 ? 1 : -1
            this.angle += this.turn.speed * backwards * 5 

            // Update position based on move speed and angle
            let phi = this.angle * (Math.PI/180)
            this.x += Math.sin(phi) * this.move.speed * 10
            this.y -= Math.cos(phi) * this.move.speed * 10
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
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

            if (e.keyCode == 37) this.stop(-1,'turn')
            else if (e.keyCode == 39) this.stop(1,'turn')
            else if (e.keyCode == 38) this.stop(1,'move')
            else if (e.keyCode == 40) this.stop(-1,'move')
        })

        this.turn = {
            speed: 0,
            dir:0,
            pressed:{'-1':false,'1':false},
            tween: null
        }
        this.move = {
            speed: 0,
            dir:0,
            pressed:{'-1':false,'1':false},
            tween: null
        }

        this.lastFrame = Date.now()
        this.render()
    },
    methods:{

        // Starts speeding tween
        start(dir,type){
            this[type].pressed[dir] = true
            this[type].dir = dir

            if (this[type].tween) this[type].tween.stop()

            this[type].tween = new TWEEN.Tween(this[type])
                .to({speed: dir},400)
                .easing(TWEEN.Easing.Quadratic.In)
                .start()
        },

        // Slow the speed down to 0
        stop(dir,type){
            this[type].pressed[dir] = false

            // Only stop if dir is the current dir
            if (this[type].dir == dir){
                // Check if there is previous dir active
                if (this[type].pressed[dir*-1]){
                    this.start(dir*-1,type)
                }else{
                    this[type].tween.stop()
                    this[type].tween = new TWEEN.Tween(this[type])
                        .to({speed: 0},200)
                        .easing(TWEEN.Easing.Quadratic.Out)
                        .start()
                }
            }
        },

        // Update car values
        render(){

            // Recursive call
            requestAnimationFrame(this.render.bind(this))

            // Update tween values
            TWEEN.update()

            // Make the calcs more precise and the animation more fluent
            let accuracy = (Date.now() - this.lastFrame) / (1000/60)

            if (this.turn.speed){

                // Update angle based on turn speed
                let relSpeed = this.move.speed + .3
                this.angle += this.turn.speed * accuracy * relSpeed * 5
            }

            if (this.move.speed){

                // Update position based on move speed and angle
                let phi = this.angle * (Math.PI/180)
                this.x += Math.sin(phi) * this.move.speed * accuracy * 10
                this.y -= Math.cos(phi) * this.move.speed * accuracy * 10

                this.x = Math.min(Math.max(this.x,0),window.innerWidth)
                this.y = Math.min(Math.max(this.y,0),window.innerHeight)
            }

            this.lastFrame = Date.now()
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
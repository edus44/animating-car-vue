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
        window.addEventListener('keydown',e=>{
            if (e.keyCode == 37) this.turn(-1)
            else if (e.keyCode == 39) this.turn(1)
        })
    },
    methods:{
        turn(dir){
            this.angle += dir * 10
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
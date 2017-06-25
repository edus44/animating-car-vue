<template>
    <svg>
        <use 
            :xlink:href="carHref"
            :width="width"
            :height="height" 
            :x="x" 
            :y="y" 
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
        y:100
    })
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
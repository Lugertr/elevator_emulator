<template>
    <div class="btnArea">
        <span>{{this.floorInfo.numb}}</span>
        <button class="btn" @click="this.btnclick" 
            v-bind:class="{btnActive: this.floorInfo.called}"></button>
    </div>
</template>
<script>
export default {
    props:{
        floorInfo: {type:Object},
        btnFunc:{type: Function}
    },
    methods: {
        btnclick() {
            if (!this.floorInfo.called)
                this.btnFunc(this.floorInfo.numb)}
    },
}
</script>
<style scoped>
    .btnArea{
        height: 100px;
        width: 80px;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .btn{
        width: 25px;
        height: 25px;
        background-color: aqua;
        cursor: pointer;
    }

    .btn::before{
        position: absolute;
        content: "";
        width: 10px;
        height: 10px;
        margin-left: -7px;
        margin-top: -7px;
        border-radius: 50%;
        border: 2px solid black;
    }

    .btnActive{
        background-color: brown;
    }

    .btnActive:active{
        transform: rotate(360deg);
    }
</style>
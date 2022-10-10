<template>
    <div class="btnArea">
        <span>{{this.floorNumb}}</span>
        <button class="btn" @click="this.btnclick" 
            v-bind:class="{btnActive: this.btnActive}"></button>
    </div>
</template>
<script>
export default {
    props:{
        floorNumb: {type:Number},
        btnFunc:{type: Function}
    },
    data() {
        return {
            btnActive: false,
        }
    },
    methods: {
        btnclick() {
            if (!this.btnActive) {
                this.btnActive = true;
                this.btnFunc(this.floorNumb)
            }
        }
    }
}
</script>
<style scoped>
    .btnArea{
        height: 100px;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .btn{
        width: 25px;
        height: 25px;
        background-color: bisque;
        cursor: pointer;
    }

    .btnActive{
        background-color: brown;
    }
</style>
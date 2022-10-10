<template>
    <div>
        <span>{{this.btnStatus.floor}}</span>
        <button></button>
    </div>
</template>
<script>
export default {
    props:{
        btnStatus: {type:Object}
    },
    methods: {
        btnclick() {
            if (!this.active) {
                this.active = true;
                
            }
        }
    }
}
</script>
<style scoped>
    
</style>
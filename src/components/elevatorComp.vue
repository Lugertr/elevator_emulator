<template>
    <div class="elevator" @click="this.action"  v-bind:class="{flashing: this.blink}">
        
    </div>
</template>
<script>

export default {
    props:{
        status:{ type:Object},
        moveOp:{ type:Function}
    },
    data() {
        return {
            blink: false,
        }
    },
    methods:{
        async action() {
            //if (this.status.move && this.status.query.length) {
            if (!this.blink) {
                this.blink= !this.blink;
                return new Promise((resolve)=>{
                    setTimeout(()=>{
                        this.blink= !this.blink;
                        resolve()
                    },3000)
                })
            }
            return 
        },
        async blinc() {
            this.blink = false;
            return new Promise((resolve)=>{
                    setTimeout(()=>{
                        this.blink= !this.blink;
                        resolve()
                    },3000)
                })
        },
        
    }
}
</script>
<style scoped>
    .elevator{
        width: 80px;
        height: 100px;
        background-color: blue;
    }

    .flashing{
        animation-name: blinker;
        animation-duration: 1s;
        animation-iteration-count: infinite;
    }


    @keyframes blinker {
        from { opacity: 1.0; }
        to { opacity: 0.2; }
    }
</style>
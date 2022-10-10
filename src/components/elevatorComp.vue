<template>
    <div class="elevator" @click="this.action"
    :style="this.postion"  
    v-bind:class="{flashing: this.blink}">
        <div v-show="this.move"></div>
    </div>
</template>
<script>

export default {
    props:{
        status:{ type:Object},
        getFloor: {type: Function},
    },
    data() {
        return {
            blink: false,
            move: false,
            floorToMove: 1,
            floorNumbs: 0,
            postion: {
            }
        }
    },
    methods:{
        async action() {
            if (!this.blink) {
                while(this.floorToMove = this.getFloor()) {
                    this.floorNumbs = Math.abs(this.floorToMove-this.status.floor);
                    await this.moving().then(()=>this.blinc())
                }
            }
            return 
        },
        async moving() {
            console.log(this.floorNumbs)
            this.postion.transition = `${this.floorNumbs}s`;
            this.postion.marginTop = `${100*(this.floorToMove-1)}px`
            return new Promise(resolve=>setTimeout(()=>{
                resolve();
            },this.floorNumbs*1000))
            
        },
        async blinc() {
            this.blink = true;
            return new Promise((resolve)=>{
                    setTimeout(()=>{
                        this.blink= !this.blink;
                        this.status.floor = this.floorToMove;
                        resolve()
                    },3000)
                })
        },
    },
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
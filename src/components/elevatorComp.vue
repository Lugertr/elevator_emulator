<template>
    <div class="elevator"
    :style="this.position"  
    v-bind:class="{flashing: this.blink}">
        <div class="table" v-show="this.move">
            <span>{{this.floor}}</span>
            <span>{{this.direction}}</span>
        </div>
    </div>
</template>
<script>
import { takeWhile } from 'rxjs';


export default {
    props:{
        floor: {type:Number},
        call:{type:Boolean},
        id:{type:Number},
        //status:{ type:Object}, 
        getFloor: {type: Function},
    },
    data() {
        return{
            direction: "△",
            pathLength: 0,
            blink: false,
            move: false,
            moveTo: 0,
            position: {},
        }
    },
    methods:{
        async action() {
            this.$emit('saveState')
            this.move = true;
            console.log([this.floor])
            
            while(this.moveTo = ( this.getFloor(this.id) || 0)) {
                this.pathLength = this.moveTo-this.floor;
                //this.floor = this.moveTo;
                //console.log(this.floor)
                this.direction = (this.pathLength>=0) ? "△": "▽";
                this.$emit('changeFloor',this.moveTo,this.id)
                await this.moving().then(()=>{                                          //начало движения
                    this.$emit('moveOver',this.moveTo);        //сигнал родителю, что движение закончено
                    return this.blinc()})                                               // мигание
                    .then(()=>this.$emit('saveState'))
                }
            this.move = false;                                                   //возврат лифта к состоянию покоя
            //this.call=false;
            this.$emit('changeCall',false,this.id)
            this.$emit('saveState')
        },
        async moving() {
            return new Promise((resolve)=>{
                const eventHandler = () => {                                            //Обработчик для удаления события у лифта
                    this.$el.removeEventListener("transitionend",eventHandler)
                    resolve()}
                console.log(this.pathLength)
                this.position = {transitionDuration: `${Math.abs(this.pathLength)}s`,
                                marginBottom:`${100*(this.moveTo-1)}px`}    
                //this.position.transitionDuration = `${Math.abs(this.pathLength)}s`; //Длительность движения
                //this.position.marginBottom = `${100*(this.moveTo-1)}px`    //MarginBottom как процесс движения
                this.$el.addEventListener("transitionend",eventHandler,true);
            })
            
        },
        async blinc() {
            this.blink = true;
            return new Promise((resolve)=>{
                const eventHandler = () => {
                    this.$el.removeEventListener("animationend",eventHandler)   
                    this.blink=false;
                    resolve()}
                    this.$el.addEventListener("animationend", eventHandler,true);
                })
        },
        changeProps() {
            this.$emit('changeFloor',this.moveTo,this.id)
            this.$emit('saveState');
        }
    },
    created() {
        this.position = {
            transitionDuration:'0s',
            marginBottom: `${100*(this.floor-1)}px`};
    },
    mounted() {
        if (this.call)
            setTimeout(()=>this.action(),0)
        this.$watch('call', (call) => {
            if (call && !this.move)
                this.action()
    });
  },
}
</script>
<style scoped>
    .elevator{
        width: 80px;
        height: 100px;
        background-color: blue;
        transition-timing-function: linear;
        transition-property: margin-bottom;
    }

    .flashing{
        animation-name: blinker;
        animation-duration: 1s;
        animation-iteration-count: 3;
    }


    @keyframes blinker {
        from { opacity: 1.0; }
        to { opacity: 0.2; }
    }

    .table{
        font-size: 30px;
        color: white;
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
</style>
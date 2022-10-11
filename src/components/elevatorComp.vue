<template>
    <div class="elevator"
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
            blink: false, //Анимация мигания
            move: false,    //Проверка на движение
            floorToMove: 1, //Номер этажа куда лифт движется
            floorNumbs: 0, //Количество этажей, которые нужно пройти
            postion: {}
        }
    },
    methods:{
        async action() {
            this.move = true;

            while(this.floorToMove = (this.getFloor() || 0)) {

                this.floorNumbs = Math.abs(this.floorToMove-this.status.floor);

                await this.moving().then(()=>{  //начало движения
                    this.$emit('moveOver',this.floorToMove, true); //сигнал родителю, что движение закончено
                    return this.blinc()}) // мигание

                }

            this.move = false;          //возврат лифта к состоянию покоя
            this.status.call=false;
        },
        async moving() {
            return new Promise((resolve)=>{
                const eventHandler = () => {    //Обработчик для удаления события у лифта
                    this.$el.removeEventListener("transitionend",eventHandler)
                    resolve()}

                this.postion.transitionDuration = `${this.floorNumbs}s`;    //Длительность движения
                this.postion.marginBottom = `${100*(this.floorToMove-1)}px`    //MarginBottom как процесс движения
                this.$el.addEventListener("transitionend",eventHandler);
            })
            
        },
        async blinc() {
            this.blink = true;
            return new Promise((resolve)=>{
                const eventHandler = () => {
                    this.$el.removeEventListener("animationend",eventHandler)   
                    this.blink=false;
                    resolve()}

                    this.status.floor = this.floorToMove;
                    this.$el.addEventListener("animationend", eventHandler);
                })
        },
    },
    watch:{
        "status.call": function(call) {
            if (call && !this.move)
                this.action()
        }
    }
}
</script>
<style scoped>
    .elevator{
        width: 80px;
        height: 100px;
        background-color: blue;
        transition-timing-function: linear;
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
</style>
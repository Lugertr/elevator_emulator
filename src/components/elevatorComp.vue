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

export default {
    props:{
        floor: {type:Number},           //этаж лифта
        call:{type:Boolean},            //вызван ли лифт
        id:{type:Number},               
        getFloor: {type: Function},     //получить очередной этаж из очереди задач
    },
    data() {
        return{
            direction: "△",             //символ направления
            pathLength: 0,              //Количество этажей, которые лифту нужно пройти
            blink: false,               //должен ли лифт мигать
            move: false,                //движется ли лифт
            moveTo: 0,                  //к какому этажу лифт движется
            position: {},               //расположение лифта относительно страницы
        }
    },
    methods:{
        async action() {                                            //функция работы лифта
            this.$emit('saveState',this.id)                         //сохранение состояния
            this.move = true;                                       
            
            while(this.moveTo = ( this.getFloor(this.id) || 0)) {   //обратка очереди
                this.pathLength = this.moveTo-this.floor;           //вычисление длины пути

                this.direction = (this.pathLength>=0) ? "△": "▽";   //определение стреки табло
                this.$emit('changeFloor',this.moveTo,this.id)
                await this.moving().then(()=>{                        //начало движения
                    this.$emit('moveOver',this.moveTo);               //сигнал родителю, что движение закончено
                    return this.blinc()})                             // мигание
                    .then(()=>this.$emit('saveState',this.id))        //сохранение состояния
                }
            this.move = false;                                        //возврат лифта к состоянию покоя
            this.$emit('changeCall',false,this.id)                    //сигнал,что работа лифта закончина                  
            this.$emit('saveState',this.id)                           //сохранить состояние
        },
        async moving() {                                                                //функция движения
            return new Promise((resolve)=>{
                const eventHandler = () => {                                            //Обработчик для удаления события у лифта
                    this.$el.removeEventListener("transitionend",eventHandler)
                    resolve()}

                this.position = {transitionDuration: `${Math.abs(this.pathLength)}s`,   //Длительность движения
                                marginBottom:`${100*(this.moveTo-1)}px`}                //MarginBottom как процесс движения
    
                this.$el.addEventListener("transitionend",eventHandler,true);           //событие для прекращения анимации движения
            })
            
        },
        async blinc() {                                                             //функция мигания лифта
            this.blink = true;
            return new Promise((resolve)=>{
                const eventHandler = () => {
                    this.$el.removeEventListener("animationend",eventHandler)       //обработчик для удаления события анимации мигания
                    this.blink=false;
                    resolve()}

                    this.$el.addEventListener("animationend", eventHandler,true);   //добавления события окончания анимации мигания
                })
        },
    },
    created() {                                            //Определение позиции лифта при создании компонента
        this.position = {                                   
            transitionDuration:'0s',
            marginBottom: `${100*(this.floor-1)}px`};
    },
    mounted() {                                             
        if (this.call)                                    //вызов функции работы лифта при монтировании
            setTimeout(()=>this.action(),0)
        this.$watch('call', (call) => {                   //watcher для вызова функции работы лифта
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
        display: flex;
        align-items: center;
        justify-content: center;
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
        font-size: 22px;
        background-color: blueviolet;
        border-radius: 50%;
        border: 1px solid white;
        color: white;
        width: 70%;
        height: 70%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
</style>
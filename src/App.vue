<template>
  <div class="elevArea">
    <elevatorComp 
      :getFloor="queueRemove"
      :status="ElevStatus"
      @moveOver="this.ElevatorMoveOver"/>
  </div>
  <div class="floorsArea">
      <btnComp
          v-for="obj in createBtnsProps" 
          :floorInfo="obj" :key="obj.numb"
          :btnFunc="this.elevatorCall"/>
  </div>
</template>

<script>
import elevatorComp from '@/components/elevatorComp'
import btnComp from '@/components/btnComp'

export default {
  name: 'App',
  data() {
    return{
      ElevStatus:{  //информация о лифте
        floor: 1,    //его этаж
        queue: [],  //его очередь этажей
        call:false, //проверка: вызван ли лифт
      },
      appConfig:{floors: 5}, //количество этажей
      btnsProps: [],
    }
  },
  computed: { 
    createBtnsProps: function() {                       //Генерация объектов с информацией о этаже (его номер и требуется ли ему лифт)
      for (let i=this.appConfig.floors;i>0;i--) {
        this.btnsProps.push({numb:i, called:false})
      }

      return this.btnsProps
    }
  },
  methods: {
    queueAdd(floor) {this.ElevStatus.queue.push(floor)},  //Добавление этажа в очередь

    queueRemove() {return this.ElevStatus.queue.shift()}, //Получение из очереди этажа для лифта

    elevatorCall(floor) {
      if (this.ElevStatus.floor!==floor &&     // Если лифт находится на другом этаже
      !this.ElevStatus.queue.includes(floor))  //и у лифта в очереди нет этого этажа
      {
        this.btnsProps[this.btnsProps.length-floor].called = true;  //Поменять цвет кнопки
        this.queueAdd(floor)                    //Добавить лифт в очередь
        this.ElevStatus.call = true;           
      }
    },

    ElevatorMoveOver(numb) {                    //Обработка сигнала от лифта, что он приехал на этаж
      if (this.btnsProps[this.btnsProps.length-numb])
        this.btnsProps[this.btnsProps.length-numb].called = false;}

  },
  components: {elevatorComp, btnComp}
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
  #app{
    display: flex;
    flex-direction: row;
  background-color: white;
  width: 100%;
  height: 100%;
    border: 15px solid;
  background: linear-gradient( rgb(255, 255, 255) 50%, transparent 50%);
  background-size: 100% 200px;
  background-color: tomato;
  }

  .elevArea{
    padding-left: 5px;
    padding-right: 5px;
    border-left: 2px solid gray;
    display: flex;
    align-items: flex-end;
  }

  .floorsArea{
    border-left: 4px solid gray;
    display: flex;
    flex-direction: column;
  }
</style>

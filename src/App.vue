<template>
  <div class="elevArea" v-for="(obj,index) in createElevators" >
      <elevatorComp 
          :getFloor="queueRemove"
          v-model:floor="obj.floor" v-model:call="obj.call" 
          v-model:id="obj.id" :key="index"
          @changeFloor="chageElevatorFloor"
          @changeCall="chageElevatorCall"
          @saveState="addInLocalStorage"
          @moveOver="elevatorMoveOver"/>
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
import appConfig from '@/data/appConfig'

export default {
  name: 'App',
  data() {
    return{
      appConfig: appConfig, 
      btnsProps: [],
      elevProps: []
    }
  },
  computed: { 
    createElevators: function() {
      //localStorage.clear()
      if (localStorage.getItem('elevProps')) {
        this.elevProps = JSON.parse(localStorage.getItem('elevProps'))

      return this.elevProps;
      }
      for (let i=0;i<this.appConfig.elevators;i++) 
          this.elevProps.push({floor:1,             //добавление информации о лифте: этаж на котором он находится
                              call:false,           //его очередь этажей
                              queue:[],             //проверка: вызван ли этот лифт
                              id: i})               
      return this.elevProps
    },
    createBtnsProps: function() {                       //Генерация объектов с информацией о этаже (его номер и требуется ли ему лифт)

      if (localStorage.getItem('btnsProps')) {
        this.btnsProps = JSON.parse(localStorage.getItem('btnsProps'))
      return this.btnsProps;
      }
      for (let i=this.appConfig.floors;i>0;i--) {
        this.btnsProps.push({numb:i, called:false})
      }

      return this.btnsProps
    }
  },
  methods: {
    addInLocalStorage() {
      localStorage.setItem('elevProps', JSON.stringify([...this.elevProps]));
      localStorage.setItem('btnsProps', JSON.stringify([...this.btnsProps]));
     // console.log(localStorage)
    },

    queueAdd(floor,id) {this.elevProps[id].queue.push(floor)},  //Добавление этажа в очередь
    queueRemove(id) {return this.elevProps[id].queue.shift()}, //Получение из очереди этажа для лифта
    chageElevatorFloor(floor,id) {this.elevProps[id].floor = floor},
    chageElevatorCall(call,id) {this.elevProps[id].call = call},

    elevatorFinder(floor) {
      let filtredAndSortArr = this.elevProps.filter((el)=>!el.call);   //ищем свободные лифты

      if (filtredAndSortArr.length)  {                                     //если такие имеются
        return [...filtredAndSortArr].sort((prev,next)=>                        //сортировкой находим
          Math.abs(floor-prev.floor) - Math.abs(floor-next.floor))[0].id}  //максимально близкие
      
      filtredAndSortArr = [...this.elevProps].sort((prev,next) =>
                  prev.queue.length - next.queue.length)
      return filtredAndSortArr[0].id             // если свободных нет, возвращаем случайный)
    },

    elevatorsOntheFloorChek(floor) {
      return this.elevProps.filter((el)=>(el.floor==floor && !el.call)).length  //поиск лифтов на этаже в состоянии покоя
    },

    elevatorCall(floor) {
      const id = this.elevatorFinder(floor);
      if (!this.elevatorsOntheFloorChek(floor))  //если на этаже лифты в состоянии покоя или движущиеся к этому этажу
      {
        this.btnsProps[this.btnsProps.length-floor].called = true;  //Поменять цвет кнопки
        this.queueAdd(floor,id)                                     //Добавить лифт в очередь
        this.elevProps[id].call = true;       
      }
    },

    elevatorMoveOver(numb) {                    //Обработка сигнала от лифта, что он приехал на этаж
      if (this.btnsProps[this.btnsProps.length-numb])
        this.btnsProps[this.btnsProps.length-numb].called = false;
      }
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
  background-color: PowderBlue;
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

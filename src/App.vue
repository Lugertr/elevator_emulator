<template>
  <div class="elevArea" v-for="(obj,index) in elevProps" >
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
          v-for="obj in btnsProps" 
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
      appConfig: appConfig, //конфиг количества лифтов и этажей
      btnsProps: [],        //массив, хранящий информацию о этажах
      elevProps: []         //массив, хранимый информацию о лифтах
    }
  },
  beforeMount() { 

    const prefConf = JSON.parse(localStorage.getItem('Conf'));              //получение конфига прошлого состояния
    const elevatorStorage = JSON.parse(localStorage.getItem('elevProps'));  //получение прошлого состояния лифтов

    if (prefConf && elevatorStorage &&                                      //если конфиг не менялся
        this.appConfig.elevators === elevatorStorage.length &&              //и прошлое состояние существует,
        this.appConfig.floors === prefConf.floors)                          
    {
      this.elevProps = elevatorStorage;                                     //То, состояние остается прежним
    }
    else {
      localStorage.clear();
      localStorage.setItem('Conf',JSON.stringify(this.appConfig))           //иначе формируется новое:

      for (let i=0;i<this.appConfig.elevators;i++) 
          this.elevProps.push({floor:1,             //добавление информации о лифте: этаж на котором он находится
                              call:false,           //его очередь этажей
                              queue:[],             //проверка: вызван ли этот лифт
                              id: i})               
          }

      for (let i=this.appConfig.floors;i>0;i--) {                          //заполениня массива этажей информацией о этажах
        this.btnsProps.push({numb:i, called:false})
      }

      const workEl = this.elevProps.filter(el=>el.queue.length)           //Массив лифтов с непустой очередью
      const arrOfActiveBtn = workEl.reduce((arr,el)=>{                    //Получение массива этажей, ждущих лифта
        arr.push(...el.queue)
        return arr
      },[])

      for (let btnNumb of arrOfActiveBtn) {                                 //изменение информации о этажах
        if (btnNumb<=this.appConfig.floors                                   //если они находятся в очереди у какого-либо лифта
        && this.btnsProps[this.btnsProps.length-btnNumb])            
            this.btnsProps[this.btnsProps.length-btnNumb].called = true;
      }
  },
  methods: {
    addInLocalStorage(id, addInQueue = 0)                                   //функция сохранения состояния отдельного лифта
    { const elevatorStorage = JSON.parse(localStorage.getItem('elevProps'));  // в LocalStorage
      if (elevatorStorage)
        {
          if (addInQueue===0)
              elevatorStorage[id] = {...this.elevProps[id]};
          else {                                                             //Добавление в массив очереди этажа
            elevatorStorage[id].queue.push(addInQueue)
            elevatorStorage[id] = {...elevatorStorage[id]};
          }
          localStorage.setItem('elevProps', JSON.stringify(elevatorStorage));
        }
      else localStorage.setItem('elevProps', JSON.stringify(this.elevProps));

    },
    queueAdd(floor,id) {this.elevProps[id].queue.push(floor)},                //Добавление этажа в очередь
    queueRemove(id) {return this.elevProps[id].queue.shift()},                //Получение из очереди этажа для лифта
    chageElevatorFloor(floor,id) {this.elevProps[id].floor = floor},          //Изменение этажа, на котором находится лифт
    chageElevatorCall(call,id) {this.elevProps[id].call = call},              //Изменения состояния вызова лифта

    elevatorFinder(floor) {
      let filtredAndSortArr = this.elevProps.filter((el)=>!el.call);   //ищем свободные лифты

      if (filtredAndSortArr.length)  {                                     //если такие имеются
        return [...filtredAndSortArr].sort((prev,next)=>                   //сортировкой находим
          Math.abs(floor-prev.floor) - Math.abs(floor-next.floor))[0].id}  //максимально близкие
      
      filtredAndSortArr = [...this.elevProps].sort((prev,next) =>
                  prev.queue.length - next.queue.length)
      return filtredAndSortArr[0].id                                        // если свободных нет, возвращаем случайный)
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
        this.addInLocalStorage(id,floor)
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

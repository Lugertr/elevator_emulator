<template>
  <elevatorComp></elevatorComp>
  <div class="floorsArea">
      <btnComp
          v-for="n in Config.floors" 
          :floorNumb="n"  :key="n"
          :btnFunc="queueAdd"/>
  </div>
</template>

<script>
import elevatorComp from '@/components/elevatorComp'
import btnComp from '@/components/btnComp'
export default {
  name: 'App',
  data() {
    return{
      ElevStatus:{
        floor: 1,
        queue: [],
        move: false,
      },
      Config:{
        floors: 6,
      }
    }
  },
  methods: {
    queueAdd(floor) {this.ElevStatus.queue.push(floor);},
    queueRemove() {this.ElevStatus.queue.shift();}
  },
  components: {elevatorComp, btnComp}
}
</script>

<style>

  #app{
    width: 100%;
    height: 100vh;
    display: flex;
    flex-direction: row;
  }

  .floorsArea{
    display: flex;
    flex-direction: column;
  }
</style>

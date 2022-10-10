<template>
  <elevatorComp></elevatorComp>
  <button>Клик</button>
</template>

<script>
import elevatorComp from '@/components/elevatorComp'
import btnComp from '@/components/btnComp'
export default {
  name: 'App',
  data() {
    return{
      status:{
        floor: 1,
        queue: [],
        move: false,
      } 
    }
  },
  methods: {
    queueAdd(floor) {this.status.queue.push(floor);},
    queueRemove() {this.status.queue.shift();}
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
</style>

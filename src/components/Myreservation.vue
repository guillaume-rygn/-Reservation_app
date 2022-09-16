<template>
  <h2>Mes reservations</h2>
    
    <div v-for="reservation in myreservation" :key="reservation.id">
      <p>{{reservation}}</p>
      <button v-on:click="deletechoice(reservation)">delete reservation</button>

    </div>
</template>

<script>
  export default{
    name:'Myreservation',
    props:{
      myreservation: {
        type: Array
      }
    },
    methods:{
      deletechoice(reservation){
        console.log(reservation)
        this.$emit('deleteRoom', reservation)
      }
    }
  
  }
</script>

<style>
  
</style>
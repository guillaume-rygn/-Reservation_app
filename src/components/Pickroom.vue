<template>
  <div class="roomsection">
      <label v-for="room in room" :key="room.id" class="roomchoiceparent">
          <input 
            name="meetingfree"
            type="radio" 
            :value="room._id" 
            v-model="selectedRoom"
            v-on:change="updateselectedroom()"
            :id="room._id" 
          />
          <div class="roomchoice">
            <p>{{room.name}}</p>
            <p><i class="fa-solid fa-user"></i>{{room.capacity}}</p>
            <p>{{room.equipement[0].name}}</p>
          </div>
          <br/>
      </label>
  </div>
</template>

<script>
  export default{
    name:'Pickroom',
    props:{
      room: {
        type: Object
      }
    },
    data() {
      return {
        selectedRoom: null,
      };
    },
    methods:{
      updateselectedroom(){
        this.$emit('roomChoice', this.selectedRoom)
      }
    }
  
  }
</script>

<style>
  .roomchoice{
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    padding: 0 20px;
  }

  .roomsection{
    margin: 15px;
    border: 1px solid rgb(211, 211, 211);
    padding: 15px 100px;
    border-radius: 25px;
    text-align: center;
    -webkit-box-shadow: 0px 0px 15px -15px #000000; 
    box-shadow: 0px 0px 15px -15px #000000;
  }

  .roomchoice p{
    margin: 15px 10px;
  }

  .roomchoiceparent{
    display: flex;
    align-items: center;
    border-bottom: 1px solid black;
  }

  .roomchoiceparent:hover{
    background-color: aliceblue;
  }


  .roomchoiceparent input{
    margin-right: 20px;
  }
</style>
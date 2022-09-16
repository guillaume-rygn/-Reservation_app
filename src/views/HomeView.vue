<script >
  import DatePicker from 'vue-datepicker-next';
  import 'vue-datepicker-next/index.css';
  import moment from 'moment'
  import axios from "axios";

  export default {
    components: { DatePicker },
    data() {
      return {
        date: [null,null],
        name: null,
        rooms:[],
        meetingfree: [],
        reservations:[],
        seletedroom: null,
        myreservation: localStorage.getItem('myreservation')?localStorage.getItem('myreservation').split(",") : null,
      };
    },
    mounted() {
      axios.get("http://localhost:3000/api/v1/reservations")
      .then(response => {
        this.reservations = response.data
        console.log(response.data)
      });
      axios.get("http://localhost:3000/api/v1/rooms")
        .then(response => {
          this.rooms = response.data.rooms
        })
    },
    methods:{
      getData()
      {
        this.name = null;
        this.seletedroom = null;
        this.meetingfree = [];
        axios.get("http://localhost:3000/api/v1/rooms")
        .then(response => {
          this.rooms = response.data.rooms
        })
        .then(() => {
          this.rooms.map(element => {
            if(element.reservation.length === 0){
              console.log("ici")
                this.meetingfree = [...this.meetingfree, element]
            } else {      
                let a = 0;  
                element.reservation.map(reservations => {     
                    axios.get(`http://localhost:3000/api/v1/reservations/${reservations}`)
                    .then(response => {
                      console.log("début test")
                      console.log(response)
                      console.log(response.data)
                      const start_date = moment(this.date[0]).toJSON()
                      const end_date = moment(this.date[1]).toJSON()

                      if(moment(end_date).isBefore(moment(response.data.end_date).toJSON()) && moment(end_date).isAfter(moment(response.data.start_date).toJSON())){
                        a = 1
                      } else if(moment(start_date).isBefore(moment(response.data.end_date).toJSON()) && moment(start_date).isAfter(moment(response.data.start_date).toJSON())){
                        a = 1
                      } else if(moment(end_date).isBefore(moment(response.data.end_date).toJSON()) && moment(start_date).isAfter(moment(response.data.start_date).toJSON())){
                        a = 1
                      }
                    }) 
                    .then(() => {
                      if(a == 0){
                        this.meetingfree = [...this.meetingfree, element]
                      }
                    })
                })
            }
          })
          console.log("---------------------------")
          this.meetingfree.map(element => console.log(element.name))
          console.log(this.meetingfree)
        })
      },

      reservation(){
        console.log(this.seletedroom + "," + this.name + "," + this.date[0] + "," + this.date[1])

        const reservation = {
          name:this.name,
          start_date: this.date[0],
          end_date: this.date[1],
          room: this.seletedroom
        }

        console.log(reservation)

        axios.post(`http://localhost:3000/api/v1/reservations/${this.seletedroom}`, reservation)
          .then(response => {
            console.log(response)
            console.log(response.data)
            if(localStorage.getItem("myreservation")){
              localStorage.setItem('myreservation', [localStorage.getItem('myreservation'),response.data._id])
            } else {
              localStorage.setItem('myreservation',response.data._id)
            }
            
            if(this.myreservation == null){
              this.myreservation = [response.data._id]
            } else {
              this.myreservation = [...this.myreservation, response.data._id]
            }
          })
          .then(() => {
            this.name = null;
            this.seletedroom = null;
            this.meetingfree = [];
            this.date = [null,null];
          });
        
      
      },
      deletereservation(id){
        axios.delete(`http://localhost:3000/api/v1/reservations/${id}`)
          .then(response => {
            const mynewreservation = localStorage.getItem('myreservation').split(",").filter(element => element !== id)

            if(mynewreservation.length == 0){
              localStorage.removeItem('myreservation');
              this.myreservation = null
            } else {
              localStorage.setItem('myreservation', mynewreservation)
              this.myreservation = [...this.myreservation, response.data._id]
              console.log(this.myreservation)
            }
          });
      },
/*-----------------------------------------------*/
  beforeDestroy () {
    localStorage.removeItem('myreservation');
  },

/*-----------------------------------------------*/

    }
  };
</script> 
   

<template>
  <main>
  <div class="pickdate">
     <h2>Choississez votre horaire :</h2>
    <div>
      <date-picker v-model:value="date" type="datetime" valueType="format" lang="fr" range v-on:change="getData()" class="inputdate"></date-picker>
    </div>
  </div>

  <div v-if="date[0] !== null" class="roomsection">
      <label v-for="room in meetingfree" :key="room.id" class="roomchoiceparent">
          <input 
            name="meetingfree"
            type="radio" 
            :value="room._id" 
            v-model="seletedroom"
            :id="room._id" 
          />
          <div class="roomchoice">
            <p>{{room.name}}</p>
            <p><i class="fa-solid fa-user"></i>{{room.capacity}}</p>
          </div>
          <br/>
      </label>
  </div>

  <input
    type="text"
    placeholder="votre nom"
    v-model="name"
    v-if="seletedroom !== null && date !== null"
  />

  <button v-on:click="reservation()" v-if="name !== null && date !== null">Reserver la salle</button>

  <h2>Mes reservations</h2>
  
  <div v-for="reservation in myreservation" :key="reservation.id">
    <p>{{reservation}}</p>
    <button v-on:click="deletereservation(reservation)">delete reservation</button>

  </div>
  <button v-on:click="beforeDestroy()">click</button>
  <div>

  </div>

</main>
</template>

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

  main{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .inputdate{
    left: 50%;
    transform: translate(-50%);
    min-width: 200px;
    width: 100%;
  }

  .pickdate{
    width: 80%;
    margin: 15px;
    border: 1px solid rgb(211, 211, 211);
    padding: 15px 100px;
    border-radius: 25px;
    text-align: center;
    -webkit-box-shadow: 0px 0px 15px -15px #000000; 
    box-shadow: 0px 0px 15px -15px #000000;
  }
</style>



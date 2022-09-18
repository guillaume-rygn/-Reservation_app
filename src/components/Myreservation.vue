<template>
  <div class="reservationsection">
    <hr />
    <h2>Mes reservations</h2>
    

    <div
      v-for="(reservation, index) in resultReservation"
      :key="reservation.id"
      class="selectreservation"
    >
      <p>{{reservation.name}}</p>
      <div>
        <p><span>début : </span>{{ reservation.startTime }}</p>
        <p><span>fin : </span>{{ reservation.endTime }}</p>
      </div>
      <span
        v-on:click="deletechoice(reservation.id)"
        class="material-symbols-outlined trash"
        >delete</span
      >
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import "moment/dist/locale/fr";
export default {
  name: "Myreservation",
  emits: ["deleteRoom"],
  props: {
    myreservation: {
      type: Array,
    },
  },
  data() {
    return {
      rooms: [],
      reservations: [],
      nameRoom: [],
      startDate: [],
      endDate: [],
      resultReservation:[]
    };
  },
  created() {
    this.initializePage();
  },
  computed:{
    myreservationComputed() {
    let result = []
    for (let i = 0; i < this.myreservation.length; i++) {
        let objReservation = {}
        objReservation.id = this.getId(this.myreservation[i]);
        objReservation.name = this.getName(this.myreservation[i]);
        objReservation.startTime = this.getStartTime(this.myreservation[i]);
        objReservation.endTime = this.getEndTime(this.myreservation[i]);
        result.push(objReservation)
    }
    return result;
    }
  },
  methods: {
    async initializePage(){
      await axios.get("https://adlin-rest-api.herokuapp.com/api/v1/reservations").then((response) => {
        this.reservations = response.data.reservations;
      });
      await axios.get("https://adlin-rest-api.herokuapp.com/api/v1/rooms").then((response) => {
        this.rooms = response.data.rooms;
      });
      let result = []
      for (let i = 0; i < this.myreservation.length; i++) {
          let objReservation = {}
          objReservation.id = this.getId(this.myreservation[i]);
          objReservation.name = this.getName(this.myreservation[i]);
          objReservation.startTime = this.getStartTime(this.myreservation[i]);
          objReservation.endTime = this.getEndTime(this.myreservation[i]);
          result.push(objReservation)
      }
      this.resultReservation = result
    },
    deletechoice(reservation) {
      this.$emit("deleteRoom", reservation);
    },
    getId(reservation){
      const value = this.reservations.filter(
        (element) => element._id == reservation
      );
      if(value.length > 0){
        return value[0]._id
      }
      return null
    },
    getName(reservation) {
      const value = this.rooms.filter((element) =>
        element.reservation.includes(reservation)
      );
      if(value.length > 0){
        return value[0].name
      }
      return null
    },
    getStartTime(reservation) {
      const value = this.reservations.filter(
        (element) => element._id == reservation
      );
      if(value.length > 0){
        return moment(value[0].start_date)
          .locale("fr")
          .format("dddd, Do MMMM YYYY, H:mm:ss");
      }
      return null
      
    },
    getEndTime(reservation) {
      const value = this.reservations.filter(
        (element) => element._id == reservation
      );
      if(value.length > 0){
        return moment(value[0].end_date)
          .locale("fr")
          .format("dddd, Do MMMM YYYY, H:mm:ss");
      }
      return null
    }
  }
};
</script>

<style>
.reservationsection {
  width: 80%;
  margin-top: 50px;
}

.reservationsection hr {
  width: 80%;
  height: 0.5px;
  left: 50%;
  transform: translate(-50%);
}

.reservationsection h2 {
  margin: 10px 0;
  text-align: center;
}

.selectreservation {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
  gap: 20px;
}

.selectreservation p,
.selectreservation div,
.selectreservation span {
  flex-grow: 1;
}

.selectreservation p span{
  color: #00c451;
}

.trash {
  color: rgb(255, 37, 37);
  cursor: pointer;
}

.trash:hover {
  color: rgb(151, 0, 0);
}
</style>

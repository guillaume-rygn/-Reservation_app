<template>
  <div class="reservationsection">
    <hr />
    <h2>Mes reservations</h2>
    

    <div
      v-for="(reservation, index) in myreservation"
      :key="reservation.id"
      class="selectreservation"
    >
      <p>{{ getName(reservation, index) }}</p>
      <div>
        <p><span>début : </span>{{ getStartTime(reservation, index) }}</p>
        <p><span>fin : </span>{{ getEndTime(reservation, index) }}</p>
      </div>
      <span
        v-on:click="deletechoice(reservation)"
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
    };
  },
  mounted() {
    axios.get("https://reservation-app-liard.vercel.app/api/v1/reservations").then((response) => {
      this.reservations = response.data.reservations;
    });
    axios.get("https://reservation-app-liard.vercel.app/api/v1/rooms").then((response) => {
      this.rooms = response.data.rooms;
    });
  },
  methods: {
    deletechoice(reservation) {
      this.$emit("deleteRoom", reservation);
    },
    getName(reservation, index) {
      const value = this.rooms.filter((element) =>
        element.reservation.includes(reservation)
      );
      if (value[0]) {
        this.nameRoom.push(value[0].name);
        return this.nameRoom[index];
      }
    },
    getStartTime(reservation, index) {
      const value = this.reservations.filter(
        (element) => element._id == reservation
      );
      if (value[0]) {
        this.startDate.push(value[0].start_date);
        return moment(this.startDate[index])
          .locale("fr")
          .format("dddd, Do MMMM YYYY, H:mm:ss");
      }
      this.$forceUpdate(reservation);
    },
    getEndTime(reservation, index) {
      const value = this.reservations.filter(
        (element) => element._id == reservation
      );
      if (value[0]) {
        this.endDate.push(value[0].end_date);
        return moment(this.endDate[index])
          .locale("fr")
          .format("dddd, Do MMMM YYYY, H:mm:ss");
      }
    },
  },
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

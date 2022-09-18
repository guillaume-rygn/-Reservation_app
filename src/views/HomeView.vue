<script>
import DatePicker from "vue-datepicker-next";
import "vue-datepicker-next/index.css";
import moment from "moment";
import axios from "axios";
import Pickroom from "../components/Pickroom.vue";
import Inputname from "../components/Inputname.vue";
import Myreservation from "../components/Myreservation.vue";
import Checkmark from "../components/Checkmark.vue";
import Button from "../components/Button.vue";
import "../assets/datepicker.scss";

export default {
  components: {
    DatePicker,
    Pickroom,
    Inputname,
    Myreservation,
    Checkmark,
    Button,
  },
  data() {
    return {
      error: null,
      componentKey: 0,
      date: [null, null],
      name: null,
      rooms: [],
      filterResult: [],
      meetingfree: [],
      reservations: [],
      selectedroom: null,
      myreservation: localStorage.getItem("myreservation")
        ? localStorage.getItem("myreservation").split(",")
        : [],
    };
  },
  mounted() {
    axios.get("https://adlin-rest-api.herokuapp.com/api/v1/reservations").then((response) => {
      this.reservations = response.data;
    });
    axios.get("https://adlin-rest-api.herokuapp.com/api/v1/rooms").then((response) => {
      this.rooms = response.data.rooms;
    });
  },
  computed(){
    axios.get("https://adlin-rest-api.herokuapp.com/api/v1/reservations").then((response) => {
      this.reservations = response.data;
    });
    axios.get("https://adlin-rest-api.herokuapp.com/api/v1/rooms").then((response) => {
      this.rooms = response.data.rooms;
    });
  },
  methods: {
    forceRerender() {
      this.componentKey += 1;
    },
    getData() {
      (this.error = null), (this.filterResult = []);
      this.name = null;
      this.selectedroom = null;
      this.meetingfree = [];
      axios
        .get("https://adlin-rest-api.herokuapp.com/api/v1/rooms")
        .then((response) => {
          this.rooms = response.data.rooms;
        })
        .then(() => {
          this.rooms.map((element) => {
            if (element.reservation.length === 0) {
              this.meetingfree = [...this.meetingfree, element];
              this.filterResult = this.meetingfree;
            } else {
              element.reservation.map((reservations) => {
                axios
                  .get(
                    `https://adlin-rest-api.herokuapp.com/api/v1/reservations/${reservations}`
                  )
                  .then((response) => {
                    const start_date = moment(this.date[0]).toJSON();
                    const end_date = moment(this.date[1]).toJSON();

                    if (
                      moment(end_date).isBefore(
                        moment(response.data.end_date).toJSON()
                      ) &&
                      moment(end_date).isAfter(
                        moment(response.data.start_date).toJSON()
                      )
                    ) {
                      return
                    } else if (
                      moment(start_date).isBefore(
                        moment(response.data.end_date).toJSON()
                      ) &&
                      moment(start_date).isAfter(
                        moment(response.data.start_date).toJSON()
                      )
                    ) {
                      return
                    } else if (
                      moment(end_date).isBefore(
                        moment(response.data.end_date).toJSON()
                      ) &&
                      moment(start_date).isAfter(
                        moment(response.data.start_date).toJSON()
                      )
                    ) {
                      return
                    }
                    this.meetingfree = [...this.meetingfree, element];
                    this.filterResult = this.meetingfree;
                  })
              });
            }
          });
        });
    },

    reservation() {
      const reservation = {
        name: this.name,
        start_date: this.date[0],
        end_date: this.date[1],
        room: this.selectedroom,
      };

      if (this.selectedroom && this.name && this.date[0] && this.date[1]) {
        axios
          .post(
            `https://adlin-rest-api.herokuapp.com/api/v1/reservations/${this.selectedroom}`,
            reservation
          )
          .then((response) => {
            if (localStorage.getItem("myreservation")) {
              localStorage.setItem("myreservation", [
                localStorage.getItem("myreservation"),
                response.data._id,
              ]);
            } else {
              localStorage.setItem("myreservation", response.data._id);
            }
            if (this.myreservation == null) {
              this.myreservation = [response.data._id];
            } else {
              this.myreservation = [...this.myreservation, response.data._id];
            }
            this.forceRerender();
            this.name = null;
            this.selectedroom = null;
            this.meetingfree = [];
            this.date = [null, null];
          })
      } else {
        this.error = "Merci de saisir tous les champs";
      }
    },
    deletereservation(id) {
      axios
        .delete(`https://adlin-rest-api.herokuapp.com/api/v1/reservations/${id}`)
        .then(() => {
          const mynewreservation = localStorage
            .getItem("myreservation")
            .split(",")
            .filter((element) => element !== id);

          this.name = null;
          this.selectedroom = null;
          this.meetingfree = [];
          this.filterResult = [];
          this.date = [null, null];

          if (mynewreservation.length == 0) {
            localStorage.removeItem("myreservation");
            this.myreservation = null;
          } else {
            localStorage.setItem("myreservation", mynewreservation);
            this.myreservation = mynewreservation;
          }
          this.forceRerender();
        });
    },
    updateroomchoice(event) {
      this.selectedroom = event.selectedRoom;
      this.filterResult = event.filter;
    },
    updatename(nameinput) {
      this.error = null;
      this.name = nameinput;
    }
  },
};
</script>

<template>
  <main>
    <div class="pickdate">
      <div class="relative">
        <date-picker
          prefix-class="xmx"
          v-model:value="date"
          type="datetime"
          valueType="format"
          lang="fr"
          range
          v-on:change="getData()"
          class="inputdate"
          placeholder="Choississez votre horaire"
        ></date-picker>
        <Checkmark class="checkmarkdate" v-if="date[0]" />
      </div>
    </div>

    <Pickroom
      :room="filterResult"
      :meetingroom="meetingfree"
      v-if="date[0] !== null"
      v-on:roomChoice="updateroomchoice($event)"
    />

    <Inputname
      v-on:nameChoice="updatename($event)"
      v-if="selectedroom !== null && date[0] !== null"
    />

    <Button
      v-on:click="reservation()"
      v-if="name && date && selectedroom"
    ></Button>

    <p>{{ error }}</p>

    <Myreservation
      :myreservation="myreservation"
      v-on:deleteRoom="deletereservation($event)"
      :key="componentKey"
    />
  </main>
</template>

<style>
main {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.inputdate {
  left: 50%;
  transform: translate(-50%);
  min-width: 250px;
  width: 100%;
}

@media screen and (max-width: 540px) {
  .pickdate {
    width: 100% !important;
  }
}

.pickdate {
  width: 80%;
  margin-top: 15px;
  border: 1px solid rgb(211, 211, 211);
  padding: 15px 100px;
  border-radius: 10px;
  text-align: center;
  -webkit-box-shadow: 0px 0px 15px -15px #000000;
  box-shadow: 0px 0px 15px -15px #000000;
}

.relative {
  display: inline-block;
  width: 100%;
  position: relative;
}

.checkmarkdate {
  position: absolute;
  left: -40px;
  top: 6px;
}

@media screen and (max-width: 1200px) {
  .checkmarkdate {
    position: absolute;
    left: -50px;
    top: 6px;
  }
}

@media screen and (max-width: 600px) {
  .checkmarkdate {
    position: absolute;
    left: -80px;
    top: 6px;
  }
}

.validation {
  margin-top: 15px;
}
</style>

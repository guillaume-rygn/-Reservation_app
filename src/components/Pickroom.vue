<template>

<div class="tableroom">  
    <div class="resumechoice">
      <Checkmark class="checkmark" v-if="selectedRoom"/>
      <table v-if="selectedRoom !== null">
        <tbody>
          <tr>
            <td></td>
            <td>{{selectedRoom.name}}</td>
            <td><i class="fa-solid fa-user"></i> {{selectedRoom.capacity}}</td>
            <td v-if="selectedRoom.equipements.length > 0 && selectedRoom.equipements.some(element => element.name === 'TV')"><span class="material-symbols-outlined">live_tv</span></td>
            <td v-else><i class="fa-sharp fa-solid fa-circle-xmark"></i></td>
            <td v-if="selectedRoom.equipements.length > 0 && selectedRoom.equipements.some(element => element.name === 'Retro Projecteur')"><span class="material-symbols-outlined">videocam</span></td>
            <td v-else><i class="fa-sharp fa-solid fa-circle-xmark"></i></td>
          </tr>
        </tbody>
      </table>     
      <p v-else>Aucune salle de sélectionnée</p> 
      <span class="material-symbols-outlined close expand" v-on:click="removeselectedroom()" v-if="this.selectedRoom !== null">close</span>   
      <p v-if="this.toggle" class="expand" v-on:click="changeToogle()"><span class="material-symbols-outlined display">expand_more</span></p>
      <p v-else class="expand" v-on:click="changeToogle()"><span class="material-symbols-outlined display">expand_less</span></p>
    </div>
    
    <div v-if="this.toggle">
      <table class="headertable">
        <tbody>
          <tr>
            <td></td>
            <td>Nom</td>
            <td v-on:click="sortCapacity()">
              <p class="equipmentfilter">
                <span>Capacité</span>
                <svg xmlns="http://www.w3.org/2000/svg" width="15px" height="15px" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
                <path strokeLinecap="round" strokeLinejoin="round" d="M3 7.5L7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                </svg>
              </p>
            </td>
            <td v-on:click="sortTV()">
              <p class="equipmentfilter" :class="this.filterTV?'colorselect':''">
                <span>TV</span>
                <svg xmlns="http://www.w3.org/2000/svg" width="15px" height="15px" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
                <path strokeLinecap="round" strokeLinejoin="round" d="M3 7.5L7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                </svg>
              </p>
            </td>
            <td v-on:click="sortRetroProjecteur()">
              <p class="equipmentfilter" :class="this.filterVideoprojecteur?'colorselect':''">
                <span>Retro Projecteur</span>
                <svg xmlns="http://www.w3.org/2000/svg" width="22px" height="22px" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
              <path strokeLinecap="round" strokeLinejoin="round" d="M3 7.5L7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
              </svg></p>
            </td>
            <td></td>
          </tr>
        </tbody>
      </table>
      <table v-if="room.length > 0">
        <tbody>
            <tr v-for="room in room" :key="room.id" class="choicerow">
              <label>
              <td><input 
                name="meetingfree"
                type="radio" 
                ref="meetingfree"
                :value="room" 
                v-model="selectedRoom"
                v-on:change="update()"
                :id="room._id" 
              /></td>
            
              <td>{{room.name}}</td>
              <td><i class="fa-solid fa-user"></i> {{room.capacity}}</td>
              <td v-if="room.equipements.length > 0 && room.equipements.some(element => element.name === 'TV')"><i class="fa-solid fa-circle-check"></i></td>
              <td v-else><i class="fa-sharp fa-solid fa-circle-xmark"></i></td>
              <td v-if="room.equipements.length > 0 && room.equipements.some(element => element.name === 'Retro Projecteur')"><i class="fa-solid fa-circle-check"></i></td>
              <td v-else><i class="fa-sharp fa-solid fa-circle-xmark"></i></td>
              <td></td>
            </label>
            </tr>
        
        </tbody>

      </table>
      <p v-else>Aucune salle n'est disponible à cet horaire</p>
    </div>
  </div>
</template>

<script>
  import Checkmark from './Checkmark.vue';

  export default{
    name:'Pickroom',
    components: { 
      Checkmark
    },
    data() {
      return {
        toggle: true,
        filterCapacity:false,
        filterVideoprojecteur: false,
        filterTV: false
      }
    },
    mounted(){
      this.changeToogle()
      this.$refs
    },  
    props:{
      room: {
        type: Array
      },
      meetingroom:{
        type: Array
      }
    },
    data() {
      return {
        selectedRoom: null,
        filterRoomArray: []
      };
    },
    methods:{
      removeselectedroom(){
        this.uncheck();
        this.toggle = true;
        this.selectedRoom = null;
      },  
      changeToogle(){
        this.toggle = !this.toggle
        this.$forceUpdate();
      },
      update(){
        this.changeToogle()
        if(this.filterRoomArray.length > 0){
          this.$emit('roomChoice', {selectedRoom: this.selectedRoom?this.selectedRoom._id : null, filter: this.filterRoomArray})
        } else {
          this.$emit('roomChoice', {selectedRoom: this.selectedRoom?this.selectedRoom._id : null, filter: this.meetingroom})
        }
      },
      sortCapacity(){
        this.uncheck();
        this.filterCapacity = !this.filterCapacity
        let arraySort;
        if(this.filterRoomArray.length == 0){
          this.arraySort = this.meetingroom
        } else {
          this.arraySort = this.filterRoomArray
        }
        if(this.filterCapacity){
          this.arraySort.sort((a,b) => (a.capacity > b.capacity ? 1 : ((b.capacity > a.capacity) ? -1 : 0)))
        } else {
          this.arraySort.sort((a,b) => (b.capacity > a.capacity ? 1 : ((a.capacity > b.capacity) ? -1 : 0)))
        } 

        this.$forceUpdate();
        this.checked();

      },
      uncheck(){
          for (let i = 0; i < this.$refs.meetingfree.length; i++) {
            this.$refs.meetingfree[i].checked = false
            }
      },
      checked(){
        this.$nextTick(() => {
          console.log(this.$refs.meetingfree)
          let a = 0
          if(this.selectedRoom !== null){
            for (let i = 0; i < this.$refs.meetingfree.length; i++) {
              if (this.$refs.meetingfree[i].id == this.selectedRoom._id) {
                this.$refs.meetingfree[i].checked = true;
                a = 1
              } else {
                this.$refs.meetingfree[i].checked = false
              }
            }
            if(a == 0){
              this.selectedRoom = null
            }
          }
        });
      },
      sortTV(){
        this.uncheck();
        this.filterTV = !this.filterTV
        this.sort();
        this.$forceUpdate();
        this.checked();
      },
      sortRetroProjecteur(){
        this.uncheck();
        this.filterVideoprojecteur = !this.filterVideoprojecteur
        this.sort()
        this.$forceUpdate();
        this.checked();
      },
      sort(){
        this.filterRoomArray = [];

        if(this.filterTV && this.filterVideoprojecteur){
          this.meetingroom.map(room => {
            if(room.equipements.length > 0){
              if(room.equipements.some(equipment => equipment.name == "TV" && room.equipements.some(equipment => equipment.name == "Retro Projecteur"))){
                  this.filterRoomArray.push(room)
              }
            }
          })
        } else if(this.filterTV){
          this.meetingroom.map(room => {
            if(room.equipements.length > 0){
              if(room.equipements.some(equipment => equipment.name == "TV")){
                  this.filterRoomArray.push(room)
              }
            }
          })
        } else if(this.filterVideoprojecteur){
          this.meetingroom.map(room => {
            if(room.equipements.length > 0){
              if(room.equipements.some(equipment => equipment.name == "Retro Projecteur")){
                  this.filterRoomArray.push(room)
              }
            }
          })
        } else {
          this.filterRoomArray = this.meetingroom
        }
      }
    }
  }
</script>

<style>
.display{
  color:blue
}

.close {
  color:red
}

.checkmark{
  position: absolute;
  left: -5px;
  top: 15px;
}

@media screen and (max-width: 800px) {
    .checkmark{
      position : absolute;
      left: -15px;
      top: 15px;
    }
  }

@media screen and (max-width: 540px) {
  .tableroom{
  width: 100% !important;
}
}

.tableroom{
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 80%;
  margin: 15px;
  border: 1px solid rgb(211, 211, 211);
  padding: 15px 50px;
  border-radius: 10px;
  text-align: center;
  -webkit-box-shadow: 0px 0px 15px -15px #000000; 
  box-shadow: 0px 0px 15px -15px #000000;
}

.expand{
  cursor: pointer;
}

.resumechoice{
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
  position: relative;
}

.equipmentfilter{
  gap: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.colorselect{
  color:blue;
}

.equipmentfilter:hover{
  color: blue;
  cursor: pointer;
}

.filter{
  display: flex;
  justify-content: flex-start;
  align-items: center;
}

.filter p{
  margin: 0 10px 0 20px;
  padding: 5px 15px;
}

.filter p:not(:first-child){
  border: 1px solid blue;
  color: blue;
  border-radius: 20px;
  cursor: pointer;
}

.filter p:not(:first-child):hover{
  background-color: blue;
  color: white;
}

.headertable{
  border-bottom: 1px solid black;
  margin: 15px 0 7px 0;
}

table{
  width: clamp(250px, 50vw, 350px);
  table-layout: auto;
}

td:first-child{
  min-width: 8%;
}

td:nth-child(3), td:nth-child(4), td:nth-child(5){
  text-align: center;
}

td{
  margin: 8px 0;
  text-align: left;
  min-width:23%;
  word-wrap:break-word;
  height: 50px;
  vertical-align:middle;
}

tr{
  border-bottom: 1px solid black;
}

.choicerow:hover{
  background-color: aliceblue;
}

.fa-circle-xmark{
  color:rgb(250, 18, 18);
}

.fa-circle-check{
  color:rgb(0, 218, 0);
}

</style>
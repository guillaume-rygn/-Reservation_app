<template>

<div class="tableroom">     
    <table>
      <tbody>
        <tr>
          <td></td>
          <td>Nom</td>
          <td v-on:click="sortCapacity()">
            <p class="equipmentfilter">
              <span>Capacité</span>
              <svg xmlns="http://www.w3.org/2000/svg" width="15%" height="15%" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
              <path strokeLinecap="round" strokeLinejoin="round" d="M3 7.5L7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
              </svg>
            </p>
          </td>
          <td v-on:click="sortTV()">
            <p class="equipmentfilter">
              <span>TV</span>
              <svg xmlns="http://www.w3.org/2000/svg" width="15%" height="15%" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
              <path strokeLinecap="round" strokeLinejoin="round" d="M3 7.5L7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
              </svg>
            </p>
          </td>
          <td v-on:click="sortRetroProjecteur()">
            <p class="equipmentfilter"><span>Retro Projecteur</span><svg xmlns="http://www.w3.org/2000/svg" width="20%" height="20%" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6">
            <path strokeLinecap="round" strokeLinejoin="round" d="M3 7.5L7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
            </svg></p>
          </td>
          <td></td>
        </tr>
      </tbody>
    </table>
    <table>
      <tbody>
          <tr v-for="room in room" :key="room.id" class="choicerow">
            <label>
            <td><input 
              name="meetingfree"
              type="radio" 
              :value="room._id" 
              v-model="selectedRoom"
              v-on:change="update()"
              :id="room._id" 
            /></td>
          
            <td>{{room.name}}</td>
            <td><i class="fa-solid fa-user"></i>{{room.capacity}}</td>
            <td v-if="room.equipements.length > 0 && room.equipements.some(element => element.name === 'TV')"><i class="fa-solid fa-circle-check"></i></td>
            <td v-else><i class="fa-sharp fa-solid fa-circle-xmark"></i></td>
            <td v-if="room.equipements.length > 0 && room.equipements.some(element => element.name === 'Retro Projecteur')"><i class="fa-solid fa-circle-check"></i></td>
            <td v-else><i class="fa-sharp fa-solid fa-circle-xmark"></i></td>
            <td></td>
          </label>
          </tr>
       
      </tbody>

    </table>
  </div>
</template>

<script>
  export default{
    name:'Pickroom',
    data() {
      return {
        filterCapacity:false,
        filterVideoprojecteur: false,
        filterTV: false
      }
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
      update(){
        if(this.filterRoomArray.length > 0){
          this.$emit('roomChoice', {selectedRoom: this.selectedRoom, filter: this.filterRoomArray})
        } else {
          this.$emit('roomChoice', {selectedRoom: this.selectedRoom, filter: this.meetingroom})
        }
      },
      sortCapacity(){
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
      },
      sortTV(){
        this.filterTV = !this.filterTV
        this.sort()
      },
      sortRetroProjecteur(){
        this.filterVideoprojecteur = !this.filterVideoprojecteur
        console.log(this.filterVideoprojecteur)
        this.sort()
      },
      sort(){
        this.filterRoomArray = [];

        if(this.filterTV && this.filterVideoprojecteur){
          this.meetingroom.map(room => {
            if(room.equipements.length > 0){
              if(room.equipements.some(equipment => equipment.name == "TV" && room.equipements.some(equipment => equipment.name == "Retro Projecteur"))){
                  this.filterRoomArray.push(room)
                  console.log(this.filterRoomArray)
              }
            }
          })
          this.update();
        } else if(this.filterTV){
          this.meetingroom.map(room => {
            if(room.equipements.length > 0){
              if(room.equipements.some(equipment => equipment.name == "TV")){
                  this.filterRoomArray.push(room)
                  console.log(this.filterRoomArray)
              }
            }
          })
          this.update();
        } else if(this.filterVideoprojecteur){
          this.meetingroom.map(room => {
            if(room.equipements.length > 0){
              if(room.equipements.some(equipment => equipment.name == "Retro Projecteur")){
                  this.filterRoomArray.push(room)
                  console.log(this.filterRoomArray)
              }
            }
          })
          this.update();
        } else {
          this.filterRoomArray = this.meetingroom
          console.log(this.filterRoomArray)
          this.update();
        }
      }
    }
  
  }
</script>

<style>

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



.tableroom{
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 80%;
  margin: 15px;
  border: 1px solid rgb(211, 211, 211);
  padding: 15px 100px;
  border-radius: 25px;
  text-align: center;
  -webkit-box-shadow: 0px 0px 15px -15px #000000; 
  box-shadow: 0px 0px 15px -15px #000000;
}

table:first-child{
  border-bottom: 1px solid black;
  margin-bottom: 7px;
}

table{
  min-width: 250px;
  width: 100%;
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
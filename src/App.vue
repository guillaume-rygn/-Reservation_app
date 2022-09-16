<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <img alt="room isometric" class="room" src="@/assets/room.png"  />
    <h1>Réserver une salle</h1>
  </header>

  

  <RouterView />
</template>

<style scoped>
h1 {
  text-align: center;
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

header {
  width: 100%;
  height: 100%;
  line-height: 1.5;
  border-radius: 25px;
  max-height: 100vh;
  background-color: rgb(29, 255, 225);
  padding: 20px;
}

.room {
  max-width: 500px;
  width: 100%;
  display: block;
  margin: 0 auto 2rem;
}
</style>

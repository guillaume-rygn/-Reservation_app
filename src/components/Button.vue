<template>
  <button class="reservation-btn">Réserver</button>
</template>

<script>
export default {
  name: "Button",
};
</script>

<style>
.reservation-btn {
  border: none;
  margin-top: 15px;
  padding: 10px 20px;
  background: #00f165;
  border-radius: 5px;
  color: white;
  font-weight: 800;
  border: none;
  z-index: 1;
  cursor: pointer;
}
.reservation-btn:after {
  position: absolute;
  content: "";
  width: 0;
  height: 100%;
  top: 0;
  right: 0;
  z-index: -1;
  background-color: #029b1b;
  border-radius: 5px;
  box-shadow: inset 2px 2px 2px 0px rgba(255, 255, 255, 0.5),
    7px 7px 20px 0px rgba(0, 0, 0, 0.1), 4px 4px 5px 0px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}
.reservation-btn:hover {
  color: #fff;
}
.reservation-btn:hover:after {
  left: 0;
  width: 100%;
}
.reservation-btn:active {
  top: 2px;
}
</style>

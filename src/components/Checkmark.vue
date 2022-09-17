<template>
  <svg width="25px" height="25px" viewBox="0 0 133 133" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <g id="check-group" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
      <circle id="filled-circle" fill="#00f165" cx="66.5" cy="66.5" r="54.5"></circle>
      <circle id="white-circle" fill="#FFFFFF" cx="66.5" cy="66.5" r="55.5"></circle>
      <circle id="outline" stroke="#00f165" stroke-width="10" cx="66.5" cy="66.5" r="54.5"></circle>
      <polyline id="check" stroke="#FFFFFF" stroke-width="12" points="41 70 56 85 92 49"></polyline>
    </g>
  </svg>

</template>

<script>
  export default{
    name:'Checkmark'
  }
</script>

<style>

@keyframes outline {
  from {
    stroke-dasharray: 0, calc(3.1416 * 55px * 2);
  }
  to {
    stroke-dasharray: 0, calc(3.1416 * 55px * 2), 0, calc(3.1416 * 55px * 2);
  }
}

#outline {
  animation: .38s ease-in outline;
  transform: rotate(0deg);
  transform-origin: center;
  color: #00f165;
}

@keyframes circle {
  from {
    transform: scale(1);
  }
  to {
    transform: scale(0);
  }
}

#white-circle {
  animation: .35s ease-in .35s forwards circle;
  transform: none;
  transform-origin: center;
}

@keyframes check {
  from {
    stroke-dasharray: 0, 75px;
  }
  to {
    stroke-dasharray: 75px, 75px;
  }
}

#check {
  animation: .34s cubic-bezier(0.65, 0, 1, 1) .8s forwards check; 
  stroke-dasharray: 0, 75px;
}

@keyframes check-group {
  from {
    transform: scale(1);
  }
  
  50% {
    transform: scale(1.09);
  }
  
  to {
    transform: scale(1);
  }
}

#check-group {
  animation: .32s ease-in-out 1.03s check-group;
  transform-origin: center;
}

</style>
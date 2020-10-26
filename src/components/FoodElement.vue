<template>
  <div id="food-element" :style="{ gridRowStart: y, gridColumnStart: x }"></div>
</template>

<script>
  export default {
    props: {
      x: {
        type: Number,
        requiered: true,
      },
      y: {
        type: Number,
        requiered: true,
      },
    },
  }
</script>

<style>
  #food-element {
    background-color: hsl(50, 100%, 50%);
    border: 0.25vmin solid #333;
  }
</style>

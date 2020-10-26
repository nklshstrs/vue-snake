<template>
  <div id="snake-segment" :style="{ gridRowStart: y, gridColumnStart: x }"></div>
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
  #snake-segment {
    background-color: hsl(500, 100%, 50%);
    border: 0.25vmin solid #333;
  }
</style>

<template>
<div class="tile" :class="{'empty-tile': info.isEmpty}" :style="{
  'top': info.top,
  'left': info.left,
  'background-position-x': info.x,
  'background-position-y': info.y,
}">

</div>  
</template>

<script>
export default {
  name: "tile",
  props: ["info"]
}
</script>

<style lang="scss" scoped>
.tile {
  position: absolute;
  width: 25%;
  height: 25%;
  transition: all 230ms;
  z-index: 99;
  background-image: url('../assets/monks.jpg');
  background-repeat: no-repeat;
  cursor: pointer;

  &:hover {
    transform: scale(0.9);
  }

  &.empty-tile {
    z-index: 1;
    background-image: none;
    cursor: auto;
  }
}
</style>


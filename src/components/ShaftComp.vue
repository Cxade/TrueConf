<template>
  <div class="shaft" :style="{ height: numberOfFloors + '0rem' }">
    <div class="elevator" :style="{ marginBottom: floor - 1 + '0rem' }"></div>
  </div>
</template>

<script>
export default {
  name: "ShaftComp",
  props: { numberOfFloors: Number, floor: Number },
};
</script>

<style scoped lang="scss">
.shaft {
  border: 1px solid black;
  width: 7rem;
  display: flex;
  flex-direction: column-reverse;
}

.elevator {
  background-color: blue;
  height: 10rem;
}
</style>

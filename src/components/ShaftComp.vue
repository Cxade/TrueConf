<template>
  <div class="shaft" :style="{ height: numberOfFloors + '0rem' }">
    <div
      class="elevator"
      :style="{
        transitionDuration: duration + 's',
        marginBottom: floor - 1 + '0rem',
      }"
      :class="{ busy: isBusy, moving: isMoving }"
    >
      <div class="elevator__floor">{{ floor }}</div>
      {{ this.isMoving ? arrow : null }}
    </div>
  </div>
</template>

<script>
export default {
  name: "ShaftComp",
  props: {
    numberOfFloors: Number,
    floor: Number,
    isBusy: Boolean,
    isMoving: Boolean,
  },
  data() {
    return {
      duration: 1,
      movingTop: null,
    };
  },

  watch: {
    floor: {
      handler(newFloor, oldFloor) {
        const diff = newFloor - oldFloor;
        this.duration = Math.abs(diff);
        diff > 0 ? (this.movingTop = true) : (this.movingTop = false);
      },
      deep: true,
    },
  },
  computed: {
    arrow() {
      if (this.movingTop) {
        return "⬆";
      } else {
        return "⬇";
      }
    },
  },
};
</script>

<style scoped lang="scss">
.shaft {
  border-left: 1px dashed black;
  border-right: 1px dashed black;
  width: 7rem;
  display: flex;
  flex-direction: column-reverse;
}

.elevator {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  background-color: blue;
  height: 10rem;
  font-size: 5rem;
  transition-timing-function: linear;
  border-radius: 1rem;
  border: 0.4rem solid black;
  color: white;

  &__floor {
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    top: 0.6rem;
    width: 2rem;
    height: 2.2rem;
    background-color: white;
    border: 0.2rem solid black;
    border-radius: 0.3rem;
    font-size: 1.8rem;
    color: black;
  }
}

@-webkit-keyframes busy {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0.2;
  }
  100% {
    opacity: 1;
  }
}
@keyframes busy {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0.2;
  }
  100% {
    opacity: 1;
  }
}

.busy {
  -webkit-animation: busy 1s infinite both;
  animation: busy 1s infinite both;
}
</style>

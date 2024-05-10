<template>
  <div class="elevator center">
    <div class="elevator__box">
      <ShaftComp
        v-for="(elevator, index) in elevators"
        :key="index"
        :numberOfFloors="numberOfFloors"
        :floor="elevator.floor"
      />
    </div>
    <div class="elevator__buttons" :style="{ height: numberOfFloors + '0rem' }">
      <div
        class="elevator__button"
        @click="addQueue(n)"
        v-for="n in numberOfFloors"
        :key="n"
      >
        {{ n }}
      </div>
    </div>
  </div>
</template>

<script>
import ShaftComp from "@/components/ShaftComp.vue";

export default {
  name: "MainPage",
  components: { ShaftComp },
  data() {
    return {
      queue: [],
      numberOfFloors: 5,
      elevators: [
        { id: 1, floor: 1, isBusy: false },
        { id: 2, floor: 1, isBusy: false },
        { id: 3, floor: 1, isBusy: false },
        { id: 4, floor: 1, isBusy: false },
      ],
    };
  },
  watch: {
    queue: {
      handler() {
        if (this.queue.length > 0) {
          this.elevatorCall(this.availableElevators, this.queue[0]);
        }
      },
      deep: true,
    },
  },
  methods: {
    addQueue(floor) {
      const index = this.elevators.findIndex(
        (elevator) => elevator.floor === floor
      );
      if (!this.queue.includes(floor) && index === -1) {
        this.queue.push(floor);
      }
    },
    delQueue(floor) {
      const index = this.queue.indexOf(floor);
      if (index !== -1) {
        this.queue.splice(index, 1);
      }
    },
    checkNearestElevator(elevators, floor) {
      let closestElevator = null;
      let minDifference = Number.MAX_VALUE;

      for (let elevator of elevators) {
        if (!elevator.isBusy && elevator.floor !== floor) {
          let difference = Math.abs(elevator.floor - floor);
          if (difference < minDifference) {
            minDifference = difference;
            closestElevator = elevator;
          }
        }
      }

      return closestElevator;
    },

    elevatorCall(elevators, floor) {
      let closest = this.checkNearestElevator(elevators, floor);
      closest.floor = floor;
      this.delQueue(floor);
    },
  },
  computed: {
    availableElevators() {
      return this.elevators.filter((elevator) => elevator.isBusy === false);
    },
  },
};
</script>

<style scoped lang="scss">
.elevator {
  display: flex;
  justify-content: center;
  gap: 2rem;
  padding-top: 10rem;
  &__box {
    display: flex;
    gap: 5rem;
  }
  &__buttons {
    display: flex;
    flex-direction: column-reverse;
    gap: 6rem;
    width: 4rem;
    padding: 3rem 0 3rem 0;
  }
  &__button {
    display: flex;
    align-items: center;
    justify-content: center;
    user-select: none;
    color: white;
    font-size: 2rem;
    height: 4rem;
    background-color: green;
    border-radius: 2rem;
    cursor: pointer;
  }
}
</style>

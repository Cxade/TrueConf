<template>
  <div class="elevator center">
    <div class="elevator__box">
      <ShaftComp
        v-for="(elevator, index) in elevators"
        :key="index"
        :numberOfFloors="numberOfFloors"
        :floor="elevator.floor"
        :isBusy="elevator.isBusy"
        :isMoving="elevator.isMoving"
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
      numberOfFloors: 8,
      elevators: [
        { id: 1, floor: 1, isBusy: false, isMoving: false },
        { id: 2, floor: 1, isBusy: false, isMoving: false },
        { id: 3, floor: 1, isBusy: false, isMoving: false },
        { id: 4, floor: 1, isBusy: false, isMoving: false },
      ],
    };
  },
  watch: {
    queue: {
      handler() {
        this.elevatorCall();
      },
      deep: true,
    },
    availableElevators: {
      handler() {
        this.elevatorCall();
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
      let minDifference = 300;

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

    elevatorMove(elevators, floor) {
      let closest = this.checkNearestElevator(elevators, floor);
      let duration = null;
      if (closest) {
        duration = Math.abs(floor - closest.floor) * 1000;
        closest.floor = floor;
        this.isBusyChangeWait(closest, duration);
        this.delQueue(floor);
      }
    },
    elevatorCall() {
      if (this.queue.length > 0 && this.availableElevators.length > 0) {
        this.elevatorMove(this.availableElevators, this.queue[0]);
      }
    },
    isBusyChange(elevator) {
      elevator.isBusy = !elevator.isBusy;
    },
    isMovingChange(elevator) {
      elevator.isMoving = !elevator.isMoving;
    },
    isBusyChangeWait(elevator, duration) {
      this.isBusyChange(elevator);
      this.isMovingChange(elevator);

      setTimeout(() => {
        this.isMovingChange(elevator);

        setTimeout(() => {
          this.isBusyChange(elevator);
        }, 3000);
      }, duration);
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

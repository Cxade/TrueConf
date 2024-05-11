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
      <div
        class="elevator__lines_box"
        :style="{ width: elevators.length * 2 + '0rem' }"
      >
        <div class="elevator__lines" v-for="n in numberOfFloors" :key="n"></div>
      </div>
    </div>
    <div class="elevator__buttons" :style="{ height: numberOfFloors + '0rem' }">
      <div
        v-for="n in numberOfFloors"
        :key="n"
        :class="{
          elevator__button: true,
          elevator__button_busy: busyFloors.includes(n),
          elevator__button_wait: queue.includes(n),
        }"
        @click="addQueue(n)"
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
        { floor: 1, isBusy: false, isMoving: false },
        { floor: 1, isBusy: false, isMoving: false },
        { floor: 1, isBusy: false, isMoving: false },
        { floor: 1, isBusy: false, isMoving: false },
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
        this.localStorageSet("queue");
      }
    },
    delQueue(floor) {
      const index = this.queue.indexOf(floor);
      if (index !== -1) {
        this.queue.splice(index, 1);
        this.localStorageSet("queue");
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
        this.localStorageSet("elevators");
      }
    },
    elevatorCall() {
      if (this.queue.length > 0 && this.availableElevators.length > 0) {
        this.elevatorMove(this.availableElevators, this.queue[0]);
      }
    },
    isBusyChange(elevator) {
      elevator.isBusy = !elevator.isBusy;
      this.localStorageSet("elevators");
    },
    isMovingChange(elevator) {
      elevator.isMoving = !elevator.isMoving;
      this.localStorageSet("elevators");
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
    localStorageSet(key) {
      switch (key) {
        case "elevators":
          localStorage.setItem("elevators", JSON.stringify(this.elevators));
          break;

        case "queue":
          localStorage.setItem("queue", JSON.stringify(this.queue));
          break;

        case "numberOfFloors":
          localStorage.setItem(
            "numberOfFloors",
            JSON.stringify(this.numberOfFloors)
          );
          break;

        default:
          break;
      }
    },
    checkLocalStorage() {
      let elevatorsLS = JSON.parse(localStorage.getItem("elevators"));
      if (elevatorsLS) {
        elevatorsLS = elevatorsLS.map((elevator) => {
          return {
            ...elevator,
            isMoving: false,
            isBusy: false,
          };
        });
        this.elevators = elevatorsLS;
      }
      const queueLS = JSON.parse(localStorage.getItem("queue"));
      if (queueLS) {
        this.queue = queueLS;
      }
      const numberOfFloorsLS = JSON.parse(
        localStorage.getItem("numberOfFloors")
      );
      if (numberOfFloorsLS) {
        this.numberOfFloors = numberOfFloorsLS;
      }
    },
  },
  computed: {
    availableElevators() {
      return this.elevators.filter((elevator) => elevator.isBusy === false);
    },
    busyFloors() {
      return this.elevators.map((elevator) => elevator.floor);
    },
  },
  mounted() {
    this.checkLocalStorage();
  },
};
</script>

<style scoped lang="scss">
.elevator {
  display: flex;
  justify-content: center;
  gap: 2rem;
  padding-top: 10rem;
  position: relative;

  &__lines {
    &_box {
      position: absolute;
      display: flex;
      flex-direction: column;
      z-index: -10;
      left: -5rem;
    }
    height: 10rem;
    border-bottom: 1px solid rgb(88 88 88 / 25%);
  }
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
    width: 4rem;
    background-color: green;
    border-radius: 2rem;

    &:not(.elevator__button_busy, .elevator__button_wait):hover {
      outline: 0.7rem solid rgb(141, 213, 241);
      cursor: pointer;
    }

    &_busy {
      background-color: red;
    }

    &_wait {
      background-color: aqua;
    }
  }
}
</style>

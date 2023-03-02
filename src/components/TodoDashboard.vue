<template>
  <v-main>
    <v-overlay v-if="overlay" class="overlay align-center justify-center">
      <v-progress-circular
        color="primary"
        indeterminate
        size="64"
      ></v-progress-circular>
    </v-overlay>
    <v-app-bar class="nav-width" color="info">
      <div class="d-flex align-center">
        <v-app-bar-nav-icon></v-app-bar-nav-icon>
        <v-app-bar-title class="nav-title">Todo</v-app-bar-title>
      </div>
      <v-avatar>
        <img :src="profileImage" alt="profile" />
      </v-avatar>
    </v-app-bar>
    <div class="d-padding">
      <h2 class="greetings">Hello {{ userName }},</h2>
    </div>
    <div class="d-padding">
      <div class="mt-3">
        <b-card-group>
          <!-- Pending task card -->
          <b-card
            bg-variant="danger"
            text-variant="white"
            header="Pending Tasks"
            class="text-center task-card"
          >
            <b-card-text class="task-count">{{
              countPendingTodos
            }}</b-card-text>
          </b-card>
          <!-- Completed task Card -->
          <b-card
            bg-variant="success"
            text-variant="white"
            header="Completed Tasks"
            class="text-center task-card"
          >
            <b-card-text class="task-count">{{
              countCompletedTodos
            }}</b-card-text>
          </b-card>
          <!-- Chart Card -->
          <b-card
            bg-variant="light"
            header="Charts"
            class="text-center task-card"
          >
            <b-card-text
              ><DataChart
                v-if="!overlay"
                :todayData="todayData"
                :yesterdayData="yesterdayData"
                :previousdayData="previousdayData"
                :dateArray="dateArray"
            /></b-card-text>
          </b-card>
        </b-card-group>
      </div>
    </div>
    <SearchInput :searchFunction="searchFunction" />
    <TodoItems
      :todoItems="this.user.todos"
      :deleteData="this.deleteData"
      :updateDoneStatus="this.updateDoneStatus"
      @input="updateTodo"
    />
    <FloatingButton
      :currentUser="this.user"
      :updatecurrentUser="this.updateSignInUser"
      :changeOverlay="changeOverlay"
    />
    <!-- <div class="text-center">
      <v-pagination v-model="page" :length="4" circle></v-pagination>
    </div> -->
  </v-main>
</template>

<script>
import FloatingButton from "./FloatinButton.vue";
import SearchInput from "./SearchInput.vue";
import TodoItems from "./TodoItems.vue";
import DataChart from "./DataChart.vue";

export default {
  name: "TodoDashboard",
  components: {
    FloatingButton,
    SearchInput,
    TodoItems,
    DataChart,
  },
  data: () => ({
    // todayData: {},
    // yesterdayData: {},
    // previousdayData: {},
  }),
  // watch: {
  //   async todayData(newV) {
  //     console.log("changeOverlay Called ", newV);
  //     await this.$nextTick();
  //   },
  // },
  props: {
    user: {
      type: Object,
    },
    updateSignInUser: Function,
    deleteData: Function,
    updateDoneStatus: Function,
    updateTodo: Function,
    searchFunction: Function,
    dateArray: Array,
    todayData: {
      type: Object,
    },
    yesterdayData: {
      type: Object,
    },
    previousdayData: {
      type: Object,
    },
    overlay: Boolean,
    changeOverlay: Function,
    changed: Boolean,
  },
  computed: {
    profileImage() {
      return this.user.photoURL;
    },
    userName() {
      return this.user.displayName;
    },
    countPendingTodos() {
      const newArray = [];
      if (this.user.length != 0) {
        this.user.todos.map((item) => {
          if (item.done === false) {
            newArray.push(item);
          }
        });
      }

      return newArray.length;
    },
    countCompletedTodos() {
      const newArray = [];
      this.user.todos.map((item) => {
        if (item.done === true) {
          newArray.push(item);
        }
      });
      return newArray.length;
    },
  },

  mounted() {
    // this.$emit("today", this.calculateChartData(0));
    // this.$emit("yesterday", this.calculateChartData(1));
    // this.$emit("previousday", this.calculateChartData(2));
  },
  methods: {
    // calculateChartData(index) {
    //   const newArrayPending = [];
    //   const newArrayDone = [];
    //   this.user.todos.map((item) => {
    //     if (item.createdOn === this.dateArray[index]) {
    //       if (item.done === true) {
    //         newArrayDone.push(item);
    //       } else {
    //         newArrayPending.push(item);
    //       }
    //     }
    //   });
    //   if (index === 0) {
    //     let data = {
    //       pending: newArrayPending.length,
    //       completed: newArrayDone.length,
    //     };
    //     this.todayData = data;
    //   } else if (index === 1) {
    //     let data = {
    //       pending: newArrayPending.length,
    //       completed: newArrayDone.length,
    //     };
    //     this.yesterdayData = data;
    //   } else {
    //     let data = {
    //       pending: newArrayPending.length,
    //       completed: newArrayDone.length,
    //     };
    //     this.previousdayData = data;
    //   }
    // },
  },
};
</script>
<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Tilt+Neon&display=swap");

.v-toolbar__content {
  all: unset;
  // width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-inline: 1rem;
}

.v-app-bar-title__content {
  width: 4rem;
}

.greetings {
  font-family: "Tilt Neon", cursive;
  font-size: 2rem;
  margin: 1rem 0.5rem;
}

.task-card {
  border-radius: 10px !important;
  border: none;
  margin: 0 1rem;
  &:first-child {
    margin-left: 0;
  }
  &:last-child {
    margin-right: 0;
  }
}

.task-count {
  font-size: 2.5rem;
  font-weight: bold;
}

.d-padding {
  padding-inline: 1rem;
}

.overlay {
  z-index: 10;
}

@media only screen and (max-width: 40em) {
  .task-card {
    margin: 0;
  }
}
</style>

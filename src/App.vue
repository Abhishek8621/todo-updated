<template>
  <v-app class="app-padding">
    <LogIn v-if="!this.$store.state.isLoggedIn" @next="updateLogIn" />
    <TodoDashboard
      :user="currentUser"
      :updateSignInUser="updateUser"
      :deleteData="deleteData"
      :updateDoneStatus="updateDoneStatus"
      :updateTodo="updateTodo"
      :searchFunction="searchFunction"
      :dateArray="this.dateArray"
      :todayData="todayData"
      :yesterdayData="yesterdayData"
      :previousdayData="previousdayData"
      :overlay="overlay"
      :changeOverlay="changeOverlay"
    />
  </v-app>
</template>

<script>
// import HelloWorld from "./components/HelloWorld";
import TodoDashboard from "./components/TodoDashboard.vue";
import LogIn from "./components/LogIn.vue";
import { getAuth } from "firebase/auth";
import { getFirestore, doc, getDoc, runTransaction } from "firebase/firestore";
import { app } from "./firebaseInit";

export default {
  name: "App",

  components: {
    // HelloWorld,
    TodoDashboard,
    LogIn,
  },

  mounted() {
    this.updateCurrentDate();
    this.getDateArray(this.today);
    console.log(this.dateArray);
  },

  data: () => ({
    // isLoggedIn: false,
    // user: {},
    today: "",
    dateArray: [],
    todayData: {},
    yesterdayData: {},
    previousdayData: {},
    // chartChanged: false,
    overlay: false,
  }),

  computed: {
    currentUser() {
      return this.$store.state.user;
    },
  },
  methods: {
    changeOverlay() {
      this.overlay = !this.overlay;
    },
    calculateChartData(index) {
      const newArrayPending = [];
      const newArrayDone = [];
      this.chartChanged = false;

      this.$store.state.user.todos.map((item) => {
        if (item.createdOn === this.dateArray[index]) {
          if (item.done === true) {
            newArrayDone.push(item);
          } else {
            newArrayPending.push(item);
          }
        }
      });

      if (index === 0) {
        let data = {
          pending: newArrayPending.length,
          completed: newArrayDone.length,
        };
        this.todayData = data;
        // console.log(this.todayData.pending, this.todayData.completed);
      } else if (index === 1) {
        let data = {
          pending: newArrayPending.length,
          completed: newArrayDone.length,
        };
        this.yesterdayData = data;
      } else {
        let data = {
          pending: newArrayPending.length,
          completed: newArrayDone.length,
        };
        this.previousdayData = data;
      }
      this.chartChanged = true;
    },
    getDateArray(date) {
      for (var i = 2; i > -1; i--) {
        const timeStamp = new Date(date);
        timeStamp.setDate(i);
        let day = timeStamp.getDate();
        day = day < 10 ? "0" + day : day;
        let month = timeStamp.getMonth() + 1;
        month = month < 10 ? "0" + month : month;
        const year = timeStamp.getFullYear();
        const fullDate = `${year}-${month}-${day}`;
        this.dateArray.push(fullDate);
      }
    },
    updateChart() {
      this.calculateChartData(0);
      this.calculateChartData(1);
      this.calculateChartData(2);
    },
    updateCurrentDate() {
      const timeStamp = new Date();
      let day = timeStamp.getDate();
      day = day < 10 ? "0" + day : day;
      let month = timeStamp.getMonth() + 1;
      month = month < 10 ? "0" + month : month;
      const year = timeStamp.getFullYear();
      const currentDate = `${year}-${month}-${day}`;
      this.today = currentDate;
    },
    updateLogIn(value) {
      this.$store.state.isLoggedIn = true;
      // this.user = value;
      this.$store.state.user = value;
      console.log(this.$store.state.user);
      this.updateChart();
    },
    updateUser() {
      const auth = getAuth(app);
      const cUser = auth.currentUser;
      this.getUserData(cUser);
      this.updateChart();
      // this.user = currentUser;
    },
    getUserData(user) {
      const db = getFirestore(app);
      const docRef = doc(db, "users", user.email);
      getDoc(docRef).then((result) => {
        if (result.exists()) {
          // console.log(result.data());
          this.$store.state.user = result.data();
          this.updateChart();
        } else {
          this.storeData(user);
          this.user = result.data();
        }
      });
    },
    deleteData(i) {
      const db = getFirestore(app);
      const docRef = doc(db, "users", this.$store.state.user.email);
      this.changeOverlay();
      runTransaction(db, async (transaction) => {
        return await transaction.get(docRef).then((doc) => {
          const newTodos = doc.data().todos;
          newTodos.splice(i, 1);
          transaction.update(docRef, { todos: newTodos });
        });
      })
        .then(() => {
          this.updateUser();
          this.changeOverlay();
          console.log("Data deleted added");
        })
        .catch((err) => {
          console.log("error occured!", err);
        });
    },
    updateDoneStatus(i) {
      const db = getFirestore(app);
      const docRef = doc(db, "users", this.$store.state.user.email);
      this.changeOverlay();
      this.dialog = false;
      runTransaction(db, async (transaction) => {
        const doc = await transaction.get(docRef);
        const newTodos = doc.data().todos;
        newTodos[i].done = !newTodos[i].done;
        transaction.update(docRef, { todos: newTodos });
      })
        .then(() => {
          this.updateUser();
          this.changeOverlay();
          console.log("Done successfully changed");
        })
        .catch((err) => {
          console.log("error occured!", err);
        });
    },
    updateTodo(value) {
      const db = getFirestore(app);
      const docRef = doc(db, "users", this.$store.state.user.email);
      this.changeOverlay();
      this.dialog = false;
      runTransaction(db, async (transaction) => {
        const doc = await transaction.get(docRef);
        const newTodos = doc.data().todos;
        newTodos[value.index].name = value.name;
        transaction.update(docRef, { todos: newTodos });
      })
        .then(() => {
          this.updateUser();
          this.changeOverlay();
          console.log("Data updated successfully");
        })
        .catch((err) => {
          console.log("error occured!", err);
        });
    },
    searchFunction(value) {
      if (value.length === 0) {
        return this.$store.state.user.todos;
      } else {
        const newArray = [];
        this.$store.state.user.todos.filter((item) => {
          if (item.name.includes(value)) {
            newArray.push(item);
          }
        });
        this.todos = newArray;
      }
    },
  },
};
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
}

.login-part {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  margin: auto;
  height: 40rem;
  width: 30rem;
}

.logo {
  width: 10rem;
  height: auto;
  img {
    object-fit: cover;
    width: 100%;
  }
}

.login-button-group {
  margin-top: 10rem;
}

.login-heading {
  font-size: 2rem;
  font-weight: bold;
  margin-top: 1rem;
}

.login-text {
  font-size: 1.2rem;
  color: black;
}

.login-btn {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1.3rem 2rem;
}

.margin-both {
  margin-right: 0.5rem;
}

.v-card__actions > .v-btn.v-btn {
  padding: none;
}

.app-padding {
  padding: 1rem 4rem;
}

@media only screen and (max-width: 40em) {
  .app-padding {
    padding: 0;
  }
}
</style>

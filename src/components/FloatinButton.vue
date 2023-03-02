<template>
  <div>
    <v-btn
      class="mx-2 floating-btn"
      fab
      dark
      large
      color="primary"
      @click.stop="openAdd"
    >
      <v-icon dark>{{ icons.mdiPlus }}</v-icon>
    </v-btn>
    <v-dialog class="dialog-box ps-2" v-model="dialog" max-width="550" light>
      <v-card>
        <v-card-title class="text-center"
          >What I will be doing today:</v-card-title
        >
        <v-card-text>
          <v-text-field
            class="todo-input ms-auto"
            label="My Task"
            v-model="item.name"
          ></v-text-field>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            v-if="mode != 'update'"
            class="btn"
            color="green darken-1"
            text
            v-on:click="addList"
            >Add <v-icon dark right> mdi-checkbox-marked-circle </v-icon></v-btn
          >
          <v-btn
            class="btn"
            color="green darken-1"
            text
            v-on:click="updateTask"
            v-else
          >
            Update
          </v-btn>
          <v-btn color="green darken-1" text @click="dialog = false">
            Cancel
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import { mdiPlus } from "@mdi/js";
import { app } from "../firebaseInit";
import { getFirestore, runTransaction, doc } from "firebase/firestore";

export default {
  name: "FloatingButton",
  data: () => ({
    icons: {
      mdiPlus,
    },
    dialog: false,
    item: { name: "", done: false },
    mode: "",
  }),
  props: {
    currentUser: {
      type: Object,
    },
    updatecurrentUser: Function,
    changeOverlay: Function,
  },
  methods: {
    openAdd(mode) {
      this.dialog = true;
      this.mode = mode;
    },
    addList() {
      // console.log(this.item.name);
      this.openAdd("new");
      const timeStamp = new Date();
      let day = timeStamp.getDate();
      day = day < 10 ? "0" + day : day;
      let month = timeStamp.getMonth() + 1;
      month = month < 10 ? "0" + month : month;
      const year = timeStamp.getFullYear();
      const currentDate = `${year}-${month}-${day}`;
      this.changeOverlay();
      this.dialog = false;
      this.chartChanged = false;
      const data = {
        done: false,
        name: this.item.name,
        createdOn: currentDate,
      };
      const db = getFirestore(app);
      const docRef = doc(db, "users", this.currentUser.email);
      // db.collection("users")
      //   .doc(this.currentUser.email)
      //   .update({ todos: this.currentUser.todos.arrayUnion(data) });
      runTransaction(db, async (transaction) => {
        return await transaction.get(docRef).then((doc) => {
          if (!doc.data().todos) {
            transaction.set({
              todos: [],
            });
          } else {
            const newTodos = doc.data().todos;
            newTodos.push(data);
            transaction.update(docRef, { todos: newTodos });
          }
        });
      })
        .then(() => {
          this.updatecurrentUser();
          this.changeOverlay();
          this.chartChanged = true;
          console.log("Data successfully added");
        })
        .catch((err) => {
          this.changeOverlay();
          console.log("error occured!", err);
        });
    },
  },
};
</script>

<style scoped>
.floating-btn {
  position: fixed;
  right: 5rem;
  bottom: 2rem;
}
.dialog-box {
  padding: 1rem;
}
@media only screen and (max-width: 40em) {
  .floating-btn {
    right: 1rem;
    bottom: 1rem;
  }
}
</style>

<template>
  <div class="todo-list-container">
    <v-card
      v-for="(todoItem, index) in todoItems"
      :class="`${
        todoItem.done ? 'todo-item-green' : 'todo-item-red'
      } d-flex justify-space-between my-2`"
      :key="index"
    >
      <div class="d-flex">
        <v-icon> </v-icon>
        <v-card-text
          ><span
            :class="`${todoItem.done ? 'text-decoration' : ''} text-item`"
            >{{ todoItem.name }}</span
          >
          <span class="time">Created On: {{ todoItem.createdOn }}</span>
        </v-card-text>
      </div>

      <v-card-actions>
        <v-btn
          class="mx-2"
          fab
          dark
          small
          color="primary"
          @click="openAdd(index)"
        >
          <v-icon dark>{{ icons.mdiPencil }}</v-icon>
        </v-btn>
        <v-btn
          class="mx-2"
          fab
          dark
          small
          color="primary"
          @click="updateDoneStatus(index)"
        >
          <v-icon dark>{{ icons.mdiCheck }}</v-icon>
        </v-btn>
        <v-btn
          class="mx-2"
          fab
          dark
          small
          color="primary"
          @click="deleteData(index)"
        >
          <v-icon dark>{{ icons.mdiDeleteOutline }}</v-icon>
        </v-btn>
      </v-card-actions>
    </v-card>
    <v-dialog class="dialog-box ps-2" v-model="dialog" max-width="550" light>
      <v-card>
        <v-card-title class="text-center"
          >What I will be doing today:</v-card-title
        >
        <v-card-text>
          <v-text-field
            class="todo-input ms-auto"
            label="My Task"
            v-model="enteredValue"
          ></v-text-field>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            class="btn"
            color="green darken-1"
            text
            v-on:click="updateTask(index)"
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
import { mdiDeleteOutline, mdiPencil, mdiCheck } from "@mdi/js";
export default {
  name: "TodoItems",
  data: () => ({
    icons: {
      mdiPencil,
      mdiDeleteOutline,
      mdiCheck,
    },
    dialog: false,
    enteredValue: "",
    index: null,
  }),
  props: {
    todoItems: Array,
    deleteData: Function,
    updateDoneStatus: Function,
  },
  methods: {
    openAdd(i) {
      this.dialog = true;
      this.enteredValue = this.todoItems[i].name;
      this.index = i;
    },
    updateTask() {
      const newValue = this.enteredValue;
      const data = { name: newValue, index: this.index };
      // console.log(data.name, data.index);
      // console.log(this.index);
      this.$emit("input", data);
      this.dialog = false;
    },
  },
};
</script>

<style scoped>
.todo-item-red {
  border-left: 10px solid red;
}
.todo-item-green {
  border-left: 10px solid green;
}
.text-decoration {
  text-decoration: line-through;
}
.text-item {
  font-size: 1.2rem;
}
.time {
  font-size: 0.7rem;
  color: rgb(116, 116, 115);
  margin: 0 1rem;
}
</style>

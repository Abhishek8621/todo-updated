<template>
  <div class="search-field">
    <v-text-field
      clearable
      hide-details="auto"
      label="Search"
      v-model="searchInput"
    >
    </v-text-field>
    <button class="search-btn" @click="search">
      <v-icon large>{{ icons.mdiMagnify }}</v-icon>
    </button>
    <button class="search-btn" @click="clearAll">
      <v-icon large>{{ icons.mdiClose }}</v-icon>
    </button>
  </div>
</template>

<script>
import { mdiMagnify, mdiClose } from "@mdi/js";

export default {
  name: "SearchInput",
  data: () => ({
    icons: {
      mdiMagnify,
      mdiClose,
    },
    searchInput: "",
  }),
  props: {
    searchFunction: Function,
  },
  methods: {
    search() {
      this.searchFunction(this.searchInput);
    },
    clearAll() {
      this.searchInput = "";
      this.searchFunction(this.searchInput);
    },
  },
};
</script>

<style lang="scss">
.search-field {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 50%;
  margin: 2rem auto;
  border-bottom: 1px solid black;
  padding: 0 0.5rem;
}

.search-btn {
  background: transparent;
  border: none;
}
</style>

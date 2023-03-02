<template>
  <v-card class="login-part">
    <div class="logo">
      <img class="logo-imagae" src="../assets/todo-logo.png" alt="logo" />
    </div>
    <v-card-title class="login-heading">Welcome to Todo App</v-card-title>
    <v-carousel
      height="120"
      cycle
      hide-delimiter-background
      :show-arrows="false"
    >
      <v-carousel-item v-for="(slide, i) in slides" :key="i">
        <v-card-text class="text-center login-text">{{ slide }}</v-card-text>
      </v-carousel-item>
    </v-carousel>

    <v-card-actions class="login-button-group">
      <v-btn @click="register" class="primary login-btn">
        <v-icon class="margin-both">{{ icons.mdiGoogle }}</v-icon
        >Continue with google
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import { getAuth, GoogleAuthProvider, signInWithPopup } from "firebase/auth";
import { mdiGoogle } from "@mdi/js";
import { getFirestore, doc, setDoc, getDoc } from "firebase/firestore";
import { app } from "../firebaseInit";

export default {
  name: "LogIn",

  data: () => ({
    icons: {
      mdiGoogle,
    },
    slides: [
      "Create Daily Todos",
      "Sort complete and Incomplete task",
      "Delete tasks accordingly",
      "Manage it on day to day basis",
    ],
  }),

  computed: {},

  methods: {
    register() {
      const auth = getAuth(app);
      const provider = new GoogleAuthProvider();
      signInWithPopup(auth, provider)
        .then((result) => {
          // This gives you a Google Access Token. You can use it to access the Google API.
          // const credential = GoogleAuthProvider.credentialFromResult(result);
          // const token = credential.accessToken;
          // console.log(token);
          // The signed-in user info.
          // const credential = GoogleAuthProvider.credentialFromResult(result);
          // const token = credential.accessToken;
          const user = result.user;
          // console.log("access Token", token);
          //   console.log(user.accessToken);
          this.getUserData(user);
          //   this.storeData(user);
          // IdP data available using getAdditionalUserInfo(result)
          // ...
        })
        .catch((error) => {
          console.log(error);
        });
    },
    storeData(user) {
      const db = getFirestore(app);
      // const userId = user.accessToken;
      setDoc(doc(db, "users", user.email), {
        displayName: user.displayName,
        email: user.email,
        photoURL: user.photoURL,
        todos: [],
      });
    },
    getUserData(user) {
      const db = getFirestore(app);
      const docRef = doc(db, "users", user.email);
      getDoc(docRef).then((result) => {
        if (result.exists()) {
          // console.log(result.data());
          this.$emit("next", result.data());
        } else {
          this.storeData(user);
          this.$emit("next", result.data());
        }
      });
    },
  },
};
</script>

<style></style>

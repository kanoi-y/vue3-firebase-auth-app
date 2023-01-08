<script setup lang="ts">
import { auth } from "@/lib/firebase";
import {
  createUserWithEmailAndPassword,
  onAuthStateChanged,
  signInWithEmailAndPassword,
  signOut,
  User,
} from "firebase/auth";
import { onMounted, ref } from "vue";

const currentUser = ref<User | null>(null);
const emailText = ref("");
const passwordText = ref("");

const signin = () => {
  if (emailText.value == "" || passwordText.value == "") return;
  signInWithEmailAndPassword(auth, emailText.value, passwordText.value)
    .then((userCredential) => {
      // Sign In
      const user = userCredential.user;
      console.log(user);
    })
    .catch((error) => {
      const errorCode = error.code;
      const errorMessage = error.message;
      console.log(errorCode, errorMessage);
    });
};

const createAccount = () => {
  createUserWithEmailAndPassword(auth, emailText.value, passwordText.value)
    .then((userCredential) => {
      // Create Account
      const user = userCredential.user;
      console.log(user);
    })
    .catch((error) => {
      const errorCode = error.code;
      const errorMessage = error.message;
      console.log(errorCode, errorMessage);
    });
};

const signout = () => {
  signOut(auth)
    .then(() => {
      currentUser.value = null;
    })
    .catch((error) => {
      console.log(error);
    });
};

onMounted(() => {
  onAuthStateChanged(auth, (user) => {
    if (user != null) {
      currentUser.value = user;
    } else {
      currentUser.value = null;
    }
  });
});
</script>

<template>
  <div>
    <div v-if="currentUser == null">
      <div class="card mx-auto max-w-lg">
        <div>
          <input
            type="text"
            v-model="emailText"
            label="E-MAIL"
            class="input w-full max-w-xs"
          />
          <input
            v-model="passwordText"
            label="PASSWORD"
            type="password"
            class="input w-full max-w-xs"
          />
        </div>
        <button class="btn btn-primary" @click="signin">E-Mail SIgn In</button>
        <button class="btn btn-secondary" @click="createAccount">
          Sign Up
        </button>
      </div>
    </div>
    <div v-else>
      <h2>Success to sign in with firebase auth!</h2>
      <button class="btn" @click="signout">Sign Out</button>
    </div>
  </div>
</template>

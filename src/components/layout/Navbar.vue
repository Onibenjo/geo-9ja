<template>
  <div class="navbar">
    <nav class="deep-purple darken-1">
      <div class="container">
        <!-- <a href="#" class="brand-logo">
          Geo9ja
        </a> -->
        <router-link :to="{ name: 'Home' }" class="brand-logo"
          >Geo9ja</router-link
        >
        <ul class="right">
          <div v-if="!user">
            <li>
              <router-link :to="{ name: 'Login' }">Login</router-link>
            </li>
            <li>
              <router-link :to="{ name: 'Signup' }">Signup</router-link>
            </li>
          </div>
          <div v-else>
            <li>
              {{ user.email }}
            </li>
            <li>
              <a @click="logout">Logout</a>
            </li>
          </div>
        </ul>
      </div>
    </nav>
  </div>
</template>

<script>
import firebase from "@/firebase";
export default {
  name: "Navbar",
  data() {
    return {
      user: null
    };
  },
  methods: {
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => this.$router.push({ name: "Login" }))
        .catch(err => console.log(err));
    }
  },
  created() {
    firebase.auth().onAuthStateChanged(user => {
      if (user) {
        this.user = user;
      } else {
        this.user = null;
      }
    });
  }
};
</script>

<style></style>

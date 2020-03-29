<template>
  <div class="navbar">
    <nav class="deep-purple darken-1">
      <div class="container">
        <!-- <a href="#" class="brand-logo">
          Geo9ja
        </a> -->
        <router-link to="/" class="brand-logo">Geo9ja</router-link>
        <ul class="right">
          <li>
            <router-link to="/login">Login</router-link>
          </li>
          <li>
            <router-link to="/signup">Signup</router-link>
          </li>
          <li>
            <a @click="logout">Logout</a>
          </li>
        </ul>
      </div>
    </nav>
  </div>
</template>

<script>
import { logout } from "@/firebase";
export default {
  name: "Navbar",
  data() {
    return {};
  },
  methods: {
    logout() {
      logout();
    }
  }
};
</script>

<style></style>

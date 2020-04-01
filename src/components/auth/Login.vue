<template>
  <div class="login container">
    <form @submit.prevent="login" class="card-panel">
      <h2 class="center deep-purple-text">Login</h2>
      <div class="field">
        <label for="email">Email:</label>
        <input type="email" name="email" id="email" v-model="email" />
      </div>
      <div class="field">
        <label for="password">Password:</label>
        <input
          type="password"
          name="password"
          id="password"
          v-model="password"
        />
      </div>
      <p class="red-text center">{{ feedback }}</p>
      <div class="field center">
        <button class="btn deep-purple">login</button>
      </div>
    </form>
  </div>
</template>

<script>
import firebase from "@/firebase";
export default {
  data() {
    return {
      email: null,
      password: null,
      feedback: null
    };
  },
  methods: {
    login() {
      const { email, password } = this;
      if (email && password) {
        firebase
          .auth()
          .signInWithEmailAndPassword(email, password)
          .then(() => this.$router.push({ name: "Home" }))
          .catch(err => {
            this.feedback = err.message;
          });
        this.feedback = null;
      } else {
        this.feedback = "You must enter all fields";
      }
    }
  }
};
</script>

<style>
.login {
  max-width: 400px;
  margin-top: 3.5rem;
}
.login h2 {
  font-size: 2.4rem;
}
.login .field {
  margin-bottom: 1rem;
}
</style>

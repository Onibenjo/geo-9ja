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
      <div class="field">
        <label for="alias">Alias:</label>
        <input type="text" name="alias" id="alias" v-model="alias" />
      </div>
      <p class="red-text center">{{ feedback }}</p>
      <div class="field center">
        <button class="btn deep-purple">login</button>
      </div>
    </form>
  </div>
</template>

<script>
import slugify from "slugify";
import { db, firebaseAuth } from "@/firebase";
export default {
  data() {
    return {
      email: null,
      password: null,
      alias: null,
      feedback: null,
      slug: null
    };
  },
  methods: {
    login() {
      const { alias, email, password } = this;
      if (alias && email && password) {
        this.slug = slugify(alias, {
          replacement: "-",
          remove: /[$*_+~.()'"!\-:@]/g,
          lower: true
        });
        let ref = db.collection("geo-users").doc(this.slug);
        ref.get().then(doc => {
          if (doc.exists) {
            this.feedback = "This alias already exists";
          } else {
            firebaseAuth
              .signInWithEmailAndPassword(email, password)
              .then(cred => {})
              .then(() => this.$router.push({ name: "Home" }))
              .catch(err => {
                this.feedback = err.message;
              });
          }
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

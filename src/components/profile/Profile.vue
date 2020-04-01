<template>
  <div class="view-profile container">
    <div class="card profile-card" v-if="profile">
      <h2 class="deep-purple-text center">{{ profile.alias }}'s wall</h2>
      <ul class="comments collection">
        <li v-for="(comment, index) in comments" :key="index">
          <div class="deep-purple-text">{{ comment.from }}</div>
          <div class="grey-text text-darken-2">
            {{ comment.content }}
          </div>
        </li>
      </ul>
      <form @submit.prevent="addComment" class="container">
        <div class="field">
          <label for="comment">Add a comment</label>
          <input type="text" name="comment" id="comment" v-model="newComment" />
          <p v-if="feedback" class="red-text center">{{ feedback }}</p>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import firebase, { db } from "@/firebase";
export default {
  data: () => ({
    profile: null,
    comments: [],
    newComment: null,
    feedback: null,
    user: null
  }),
  methods: {
    addComment() {
      if (this.newComment) {
        db.collection("geo-comments")
          .add({
            to: this.$route.params.id,
            from: this.user.alias,
            content: this.newComment,
            time: Date.now()
          })
          .then(() => {
            this.newComment = null;
          });

        this.feedback = null;
      } else {
        this.feedback = "You must enter a comment to add it";
      }
    }
  },
  created() {
    let ref = db.collection("geo-users");
    let commentsRef = db.collection("geo-comments");
    //get current user
    ref
      .where("userId", "==", firebase.auth().currentUser.uid)
      .get()
      .then(snapshot => {
        snapshot.forEach(doc => {
          this.user = doc.data();
          this.user.id = doc.id;
        });
      });
    //getting targeted user
    ref
      .doc(this.$route.params.id)
      .get()
      .then(user => {
        this.profile = { ...user.data() };
      });
    //comments
    commentsRef
      .where("to", "==", this.$route.params.id)
      .onSnapshot(snapshot => {
        snapshot.docChanges().forEach(change => {
          if (change.type == "added") {
            this.comments = [change.doc.data(), ...this.comments];
          }
        });
      });
  }
};
</script>

<style>
.profile-card {
  padding: 1rem 0.5rem;
}
.view-profile {
  margin-top: 1rem;
}
.comments li {
  padding: 0.8rem;
  border-bottom: 1px solid #eee;
}
</style>

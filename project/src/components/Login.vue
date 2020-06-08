<template>
  <div class="login">
    <h1>Sign in</h1>
    <b-form>
      <b-form-group>
        <label for="email-input">E-mail:</label>
        <b-input id="email-input" type="email" v-model="email" required></b-input>
      </b-form-group>
      <label for="text-password">Password:</label>
      <b-input v-model="password" type="password" id="text-password" required></b-input>
      <b-button class="reddish" @click="signIn" variant="primary">Log in!</b-button>
    </b-form>
  </div>
</template>

<script>
import firebase from 'firebase';


export default {
  name: 'Login',
  props: {
    setUserSession: Function
  },
  data: () => ({
    email: '',
    password: '',
  }),
  methods: {
    signIn: function() {
      firebase.auth().signInWithEmailAndPassword(this.email, this.password)
        .then(
          () => {
            localStorage.setItem('spa-pwa-project', this.email);
            this.setUserSession(true);
            alert('Logged in!')
          },
          (err) => alert(`Error: ${err.message}`)
        )
    }
  }
}
</script>
<template>
  <div class="login">
    <h1>Create an account</h1>
    <b-form>
      <b-form-group
          id="input-group-1"
          label="Email address:"
          label-for="email-input"
          description="We'll never share your email with anyone else.">
        <b-form-input
          id="email-input" 
          type="email" 
          v-model="email"
          required></b-form-input>
      </b-form-group>
      <b-form-group>
        <label for="text-password">Password:</label>
        <b-input v-model="password" type="password" id="text-password" aria-describedby="password-help-block"></b-input>
          <b-form-invalid-feedback :state="validation">
          Your password must be 8-20 characters long.
          </b-form-invalid-feedback>
          <b-form-valid-feedback :state="validation">
          Looks Good.
          </b-form-valid-feedback>
    <b-form-text id="password-help-block">
      Your password must be 8-20 characters long, contain letters and numbers, and must not
      contain spaces, special characters, or emoji.
    </b-form-text>
    </b-form-group>
      <b-button class="reddish" @click="signUp" variant="primary">Sign in!</b-button>
    </b-form>
  </div>
</template>

<script>
import firebase from 'firebase';


export default {
  name: 'Signup',
  data: () => ({
    email: '',
    password: '',
  }),
  methods: {
    signUp: function() {
      firebase.auth().createUserWithEmailAndPassword(this.email, this.password)
        .then(
          () => alert("User created!"),
          (err) => alert(`Error: ${err.message}`)
        )
    }
  },
  computed: {
      validation() {
        return this.password.length > 7 && this.password.length < 21
      }
    }
}
</script>
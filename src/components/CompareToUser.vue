<template>
  <div>
    <Loader v-if="this.isLoading" />
    <div class="compareUsers" v-else>
      <b-form v-if="this.showInputUserDataField">
        <b-form-group>
          <label for="userData-input">User Data</label>
          <b-input id="userData" v-model="inputField" required></b-input>
        </b-form-group>
        <b-button class="reddish" @click="this.saveUserData" variant="primary">Save data!</b-button>
      </b-form>
      <div v-else class="col-12 col-sm-8 col-lg-3 m-auto p-3 shadow-lg overflow-hidden containsUsers">
        <div class="float-left">
          <h1>Username:</h1>
          <p>{{ userOne.username }}</p>
          <h1>Name:</h1>
          <p>{{ userOne.name }}</p>
          <h1>Honor:</h1>
          <p>{{ userOne.honor }}</p>
          <h1>Clan:</h1>
          <p>{{ userOne.clan }}</p>
          <h1>Rank:</h1>
          <p>{{ userOne.leaderboardPosition }}</p>
        </div>
        <div class="float-right">
          <h1>Username:</h1>
          <p>{{ userTwo.username }}</p>
          <h1>Name:</h1>
          <p>{{ userTwo.name }}</p>
          <h1>Honor:</h1>
          <p>{{ userTwo.honor }}</p>
          <h1>Clan:</h1>
          <p>{{ userTwo.clan }}</p>
          <h1>Rank:</h1>
          <p>{{ userTwo.leaderboardPosition }}</p>
        </div>
      </div>
          <b-button @click="this.backCallback">
            Back to favorites
          </b-button>
    </div>
    
  </div>
</template>

<script>
import Loader from './Loader';
import firebase from 'firebase';

export default {
  name: 'CompareToUser',
  components: {
    Loader
  },
  props: {
    userToCompare: String,
    backCallback: Function
  },
  data: () => ({
    isLoading: false,
    showInputUserDataField: false,
    hasAddedUserData: false,
    inputField: '',
    userData: '',
    userOne: {},
    userTwo: {},
  }),
  methods: {
    checkForUserData: async function() {
      const db = firebase.firestore();
      const appUser = localStorage.getItem('spa-pwa-project');
      const docRef = db.collection(appUser).doc('userData');

      this.isLoading = true;
       await docRef.get()
        .then((querySnapshot) => {
          if (querySnapshot.data()) {
            this.hasAddedUserData = true;
            this.userData = querySnapshot.data().account;
            this.showInputUserDataField = false;
            this.compareUsers();

          } else {
            this.showInputUserDataField = true;
            this.isLoading = false
          }
        })
    },
    compareUsers: async function() {
      this.isLoading = true;
       await fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/users/${this.userData}`)
        .then(res => res.json())
        .then(res => {
          this.userOne = res
        });

        await fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/users/${this.userToCompare}`)
          .then(res => res.json())
          .then(res => {
            this.userTwo = res
          })

        this.isLoading = false;

    },
    saveUserData: function() {
      const db = firebase.firestore();
      const appUser = localStorage.getItem('spa-pwa-project');
      const docRef = db.collection(appUser).doc('userData');
      docRef.set({
        account: this.inputField
      })
      alert('Data saved!');
      this.checkForUserData();
    }
  },
  mounted: function() {
    this.checkForUserData();
  }
}
</script>
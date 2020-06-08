<template>
  <Loader v-if="this.isLoading" />
  <div v-else>
    <b-form v-if="this.showInputUrlField">
      <div>
        <b-button v-if="this.changeURL" @click="this.backCallback">
            Cancel
        </b-button>
      </div>
      <b-form-group class="col-12 col-sm-8 col-lg-3 m-auto p-0 pb-3"> 
        <label for="userData-input" class="h5 rssFont"><p>Please type your RSS url here.</p></label>
        <b-input id="userData" v-model="inputField" required></b-input>
      </b-form-group>
      <b-button class="reddish" @click="this.saveRssUrl" variant="primary">Save data!</b-button>
    </b-form> 
    <div v-else> 
      <b-button @click="this.backCallback">
          X
      </b-button>
      <VueRssFeed class="mw-100" :feedUrl="feedUrl" :name="name" :limit="limit" />
      <b-button @click="this.changeRssFeedURL">
          Change Rss url
      </b-button>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase';
import VueRssFeed from "vue-rss-feed";
import Loader from "./Loader"

export default {
  name: "RssFeed",
  props: {
    backCallback: Function
  },
  components: {
    VueRssFeed,
    Loader
  },
  data: () =>  ({
    feedUrl: "",
    name: "Rss Feed", 
    limit: 1,
    isLoading: false,
    showInputUrlField: false,
    inputField: '',
    changeURL: false,
  }),
  methods: {
    changeRssFeedURL: function() {
      this.showInputUrlField = !this.showInputUrlField;
      this.changeURL = true;
    },
    checkForSavedRSS: async function() {
      const db = firebase.firestore();
      const appUser = localStorage.getItem('spa-pwa-project');
      const docRef = db.collection(appUser).doc('rssFeed');
      this.isLoading = true;
      await docRef.get()
      .then((querySnapshot) => {
        if (querySnapshot.data()) {
          this.feedUrl = `https://cors-anywhere.herokuapp.com/${querySnapshot.data().url}`;
          this.showInputUrlField = false;
          this.isLoading = false;
        } else {
          this.showInputUrlField = true;
          this.isLoading = false
        }
      })
    },
    saveRssUrl: function() {
      const db = firebase.firestore();
      const appUser = localStorage.getItem('spa-pwa-project');
      const docRef = db.collection(appUser).doc('rssFeed');
      if (this.inputField) {
        docRef.set({
          url: this.inputField
        })
        alert('URL saved!');
        this.checkForSavedRSS();
      }
      else {alert('URL cannot be empty')}
    }
  },
  mounted: function() { 
    this.checkForSavedRSS();
  }
}
</script>

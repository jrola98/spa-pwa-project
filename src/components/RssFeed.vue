<template>
  <Loader v-if="this.isLoading" />
  <div v-else>
    <b-form v-if="this.showInputUrlField">
      <b-form-group class="col-12 col-sm-8 col-lg-3 m-auto p-0 pb-3"> 
        <label for="userData-input" class="h5 rssFont"><strong>Please type your RSS url here.</strong></label>
        <b-input id="userData" v-model="inputField" required></b-input>
      </b-form-group>
      <b-button class="reddish" @click="this.saveRssUrl" variant="primary">Save data!</b-button>
    </b-form>    
    <VueRssFeed v-else :feedUrl="feedUrl" :name="name" :limit="limit"/>
  </div>
</template>

<script>
import firebase from 'firebase';
import VueRssFeed from "vue-rss-feed";
import Loader from "./Loader"

export default {
  name: "RssFeed",
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
  }),
  methods: {
    checkForSavedRSS: async function() {
      const db = firebase.firestore();
      const appUser = localStorage.getItem('spa-pwa-project');
      const docRef = db.collection(appUser).doc('rssFeed');
      this.isLoading = true;
      await docRef.get()
      .then((querySnapshot) => {
        console.log(querySnapshot.data())
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
      docRef.set({
        url: this.inputField
      })
      alert('URL saved!');
      this.checkForSavedRSS();
    }
  },
  mounted: function() { 
    this.checkForSavedRSS();
    console.log(this.feedUrl)
  }
}
</script>

// https://cprss.s3.amazonaws.com/frontendfoc.us.xml
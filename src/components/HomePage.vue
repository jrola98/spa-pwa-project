<template>
  <div>   
    <nav>
      <b-button v-if="!showRssFeed" class="float-sm-right" @click='handleChangeFormClick'>
        <span v-if="!changeView">Show Favorites</span>
        <span v-else>Back to Search</span>
      </b-button>
      <b-button v-if="!changeView" class="float-sm-right" @click='handleRSSClick'>
        <span>Show RSS feed</span>
      </b-button>
      <b-button class="float-sm-right" @click="handleUserLogout" variant="primary">Logout!</b-button>

    </nav>
    <div v-if="!changeView">
      <SearchUser :getUserData="this.getUserData" />
      <Loader v-if="isLoading" />
      <User
        v-if="isLoading === false && userData.name !== undefined"
        :userData="this.userData"/>
    </div>
    <div v-else-if="showRssFeed">
      <RssFeed :backCallback="this.handleRssBackClick" />
    </div>
    <div v-else>
      <FavoriteUsers />
    </div>
    <div v-if="!changeView && favUsersKata.length">
      <FavUsersKata
        :favUsersKata="this.favUsersKata"
      />
    </div>
  </div>
</template>

<script>
import firebase from 'firebase';
import SearchUser from './SearchUser';
import Loader from './Loader';
import User from './User';
import FavoriteUsers from './FavoriteUsers';
import FavUsersKata from './FavUsersKata';
import RssFeed from './RssFeed'

export default {
  name: 'HomePage',
  data: () => ({
    changeView: false,
    showFavoritesUsers: false,
    showRssFeed: false,
    isLoading: false,
    userData: {},
    favUsersKata: []
  }),
  components: {
    SearchUser,
    Loader,
    User,
    FavoriteUsers,
    FavUsersKata,
    RssFeed
  },
  props: {
    setUserSession: Function
  },
  methods: {
    handleRssBackClick: function() {
      this.showRssFeed = false;
      this.changeView = false;
      this.showFavoritesUsers = false;
    },
    handleRSSClick: function() {
      this.showRssFeed = !this.showRssFeed;
      this.changeView = !this.changeView;
    },
    handleChangeFormClick: function() {
      this.changeView = !this.changeView;
      this.showRssFeed = false;
    },
    handleUserLogout: function() { 
      localStorage.removeItem("spa-pwa-project")
      this.setUserSession(false);
      alert('Logged out!')
    },
    getUserData: function(user) {
      this.isLoading = true;
      fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/users/${user}`)
        .then(res => res.json())
        .then(res => {
          this.userData = res
          this.isLoading = false;
        })
    },
    getUsersKata: async function () {
        const db = firebase.firestore();
        const appUser = localStorage.getItem('spa-pwa-project');
        const dbRef = db.collection(appUser);
        const favUsersKata = this.favUsersKata;

        this.isLoading = true;
        await dbRef.get()
          .then(function (querySnapshot) {
            querySnapshot.forEach(async function (doc) {
              if (doc.data().username) {
              await fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/users/${doc.data().username}/code-challenges/completed?page=0`)
                  .then(res => res.json())
                  .then(res => {
                    favUsersKata.push({
                      username: doc.data().username,
                      kata: res.data[0]
                    })
                  })
              }
            });
          })
          .catch(er => console.log(er))
        this.isLoading = false;
      }
  },
  mounted: function() {
    this.getUsersKata();
  }
}
</script>
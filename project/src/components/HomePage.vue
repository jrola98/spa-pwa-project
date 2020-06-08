<template>
  <div>
    <nav>
      <b-button class="float-sm-right" @click='handleChangeFormClick'>
        <span v-if="!changeView">Show Favorites</span>
        <span v-else>Back to Search</span>
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
    <div v-else>
      <FavoriteUsers />
    </div>
  </div>
</template>

<script>
import SearchUser from './SearchUser';
import Loader from './Loader';
import User from './User';
import FavoriteUsers from './FavoriteUsers';

export default {
  name: 'HomePage',
  data: () => ({
    changeView: false,
    showFavoritesUsers: false,
    isLoading: false,
    userData: {}
  }),
  components: {
    SearchUser,
    Loader,
    User,
    FavoriteUsers
  },
  props: {
    setUserSession: Function
  },
  methods: {
    handleChangeFormClick: function() {
      this.changeView = !this.changeView;
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
  }
}
</script>
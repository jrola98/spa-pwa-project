<template>
  <div>
    <Loader v-if="isLoading" />
    <div class="UserDetails" v-else>
      <div class="box">
        <div>
          <p>Username: {{this.userDetails.username}}</p>
          <p>Name: {{this.userDetails.name}}</p>
          <p>Honor: {{this.userDetails.honor}}</p>
          <p>Clan: {{this.userDetails.clan}}</p>
          <p>Leadeboard position: {{this.userDetails.leaderboardPosition}}</p>
        </div>
        <div class="languagelvl">
        <p>Programming language level</p>
        <ul v-for="[key, value] of Object.entries(userDetails.ranks.languages)" :key="key">
          <div>
            <li>
              <p class="language">Language: {{key}}</p> 
              <p class="score">Score: {{value.score}}</p>
              <p class="rank">Rank: {{value.rank*(-1)}} kyu</p>
            </li>
          </div>
        </ul>
        </div>
      </div>
      <b-button @click="this.backCallback">
        Get back to favourite users!
      </b-button>
    </div>
  </div>
</template>

<script>
import Loader from './Loader';

export default {
  name: 'UserDetails',
  props: {
    backCallback: Function,
    user: String
  },
  components: {
    Loader
  },
  data: () => ({
    isLoading: false,
    userDetails: {}
  }),
  methods: {
    getUserData: async function() {
      this.isLoading = true;
      await fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/users/${this.user}`)
        .then(res => res.json())
        .then(res => {
          this.userDetails = res
          this.isLoading = false;
        })

    },
  },
  mounted: async function() {
    await this.getUserData();
    console.log(this.userDetails)

  }
}
</script>

<style lang="scss">
  .UserDetails {
    background-color: #333;
    position: absolute;
    top: 100px;
    left: 0;  
    width: 100%;
    height: 100%;
  }
  .languagelvl {
    padding: 30px 0;
  }
  .box div {
    font-family: Arial, Helvetica, sans-serif;
    box-shadow: 10px #666;
  }
  ul {
    margin: 0;
    padding: 0;
  }
  ul div {
    margin: 0 auto;
    max-width: 400px;
    list-style-type: none;
    border: 1px solid #444;
  }
  li p {
    font-family: Arial, Helvetica, sans-serif;
    font-size: 16px;
    margin: 4px; 
  }
</style>
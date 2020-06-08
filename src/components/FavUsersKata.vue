<template>
  <div>
    <h5>NewsFeed: </h5>
    <div style="display: flex; justify-content: space-between; margin: 0 auto 20px;" class="col-12 col-sm-8 col-lg-6 p-0 pb-5r" v-for="userKata in favUsersKata" :key="userKata.username">
        <p style="margin-bottom: 0">{{userKata.username}}</p>
        <b-button @click="getKata(userKata.kata.slug)">
                {{userKata.kata.name}}
        </b-button>
    </div>
    <Loader v-if ="isLoading"/>
    <div v-else-if="kataDetails.name" class="col-12 col-sm-8 col-lg-6 m-auto"
      style="border: 1px solid white;padding: 10px"
    >
      <table>
        <tbody >
          <tr>
            <td class="left"><p>KATA NAME:</p></td>
            <td class="right">{{kataDetails.name}}</td>
          </tr>
          <tr>
            <td class="left"><p>CATEGORY:</p></td>
            <td class="right">{{kataDetails.category}}</td>
          </tr>
          <tr>
            <td class="left"><p>PUBLISHED AT:</p></td>
            <td class="right">{{(new Date(kataDetails.publishedAt)).toLocaleDateString()}}</td>
          </tr>
          <tr>
            <td class="left"><p>LANGUAGES:</p></td>
            <td class="right">{{kataDetails.languages.join(', ')}}</td>
          </tr>
          <tr>
            <td class="left"><p>KATA RANK:</p></td>
            <td class="right">{{kataDetails.rank.name}}</td>
          </tr>
          <tr>
            <td class="left"><p>TOTAL ATTEMPTS:</p></td>
            <td class="right">{{kataDetails.totalAttempts}}</td>
          </tr>
          <tr>
            <td class="left"><p>TOTAL COMPLETED:</p></td>
            <td class="right">{{kataDetails.totalCompleted}}</td>
          </tr>

        </tbody>
      </table>
      <div style="width: 100%; text-align: center">
        <a :href="kataDetails.url">
          Link to kata
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import Loader from './Loader';

export default {
  name: 'FavUsersKat',
  data: () => ({
    kataDetails: {},
    isLoading: false,
  }),
  components: {
    Loader
  },
  props: {
    favUsersKata: Array
  },
  methods: {
   getKata: async function(kataSlug) {
     this.isLoading = true
     await fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/code-challenges/${kataSlug}`)
      .then(res => res.json())
      .then(res => {
        this.kataDetails = res;
      })
      this.isLoading = false
   }
  }
}
</script>

<style lang="scss">
  p {
    font-size: 10px;
    
  }

  .username {
    max-width: 50%;
    float: left;
  }
  .kataname {
    width: 50%;
  }
  .left {
  text-align: left;
  vertical-align: middle;
  min-width: 30%;
  margin: 0;
  padding: 0;
}
.right {
  text-align: right;
  vertical-align: middle;
}
  
</style>
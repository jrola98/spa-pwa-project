<template>
  <div>
    <div v-for="userKata in favUsersKata" :key="userKata.username">
      {{userKata.username}}
      <button @click="getKata(userKata.kata.slug)">
        {{userKata.kata.name}}
      </button>
    </div>
    <div v-if="kataDetails.name">
      
      {{kataDetails.name}}
      {{kataDetails.category}}
      {{kataDetails.publishedAt}}
      {{kataDetails.languages}}
      {{kataDetails.rank.name}}
      {{kataDetails.description}}
      {{kataDetails.totalAttempts}}
      {{kataDetails.totalCompleted}}
      <a :href="kataDetails.url">
        Link to kata
      </a>
    </div>
  </div>
</template>

<script>

export default {
  name: 'FavUsersKat',
  data: () => ({
    kataDetails: {},
  }),
  props: {
    favUsersKata: Array
  },
  methods: {
   getKata: async function(kataSlug) {
     await fetch(`https://cors-anywhere.herokuapp.com/https://www.codewars.com/api/v1/code-challenges/${kataSlug}`)
      .then(res => res.json())
      .then(res => {
        console.log(res)
        this.kataDetails = res;
      })
   }
  },
  mounted: function() {
    console.log(this.favUsersKata)
  }
}
</script>

// nie da się poukładać intelektem boga
// potrzebna nam jest pokora w relacji z bogiem
// 
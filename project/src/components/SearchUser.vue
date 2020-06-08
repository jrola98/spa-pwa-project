<template>
  <div>
    <b-input-group class="col-12 col-sm-8 col-lg-3 m-auto p-0 pb-3">
      <b-form-input v-model="userName" ></b-form-input>
      <b-input-group-append>
        <b-button variant="primary" @click="handleSearchClick">Search</b-button>
      </b-input-group-append>
    </b-input-group>
  </div>
</template>

<script>

export default {
  name: 'SearchUser',
  props: {
    getUserData: Function
  },
  data: () => ({
    userName: "",
  }),
  methods: {
    handleSearchClick: function() {
      this.getUserData(this.userName)
    }
  }
}
</script>
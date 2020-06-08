<template>
  <div>
    <div v-if="!image">
      <h2>Select an image for {{this.user}}</h2>
      <b-button @click="this.changeView">
        Back to user list
      </b-button>
    </div>
    <div v-else>
      <img :src="image" />
      <div>
        <b-button @click="uploadImage">
          Submit
        </b-button>
      </div>
    </div>
    <div>
      <b-form-file class="file-picker" type="file" @change="onFileChange"></b-form-file>
    </div>
  </div>
</template>

<script>
  import firebase from 'firebase';

  export default {
    name: 'EditUserImage',
    data: () => ({
      image: '',
    }),
    props: { 
      user: String,
      changeView: Function,
    },
    methods: {
      onFileChange(e) {
        const files = e.target.files || e.dataTransfer.files;
        if (!files.length)
          return;
        this.createImage(files[0]);
      },
      createImage(file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          this.image = e.target.result;
        };
        reader.readAsDataURL(file);
      },
      removeImage: function () {
        this.image = '';
      },
      
      uploadImage: function () {
        const changeView = this.changeView;
        const appUser = localStorage.getItem('spa-pwa-project');
        //get user folder
        const storageRef = firebase.storage().ref(appUser);

        //create file name in user folder
        const fileRef = storageRef.child(`${this.user}.jpg`);

        fileRef.putString(
          this.image,
          'data_url',
          {
            contentType: 'image/jpg'
          }
        ).then(() => {
          alert("Uploaded");
          changeView();
        })
        .catch(er => console.log(er))
      }
    },
  }
</script>

<style lang="scss">
  .file-picker {
    max-width: 400px;
  }
</style>
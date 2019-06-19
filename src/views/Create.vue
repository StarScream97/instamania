<template>
  <div class="create-post-form">
  <div class="container createPost">
      <form
        action="/create"
        method="post"
        enctype="multipart/form-data"
        @submit.prevent="createPost()"
      >
        <div class="field">
          <label>Title</label>
          <input name="title" v-model="title" type="text" placeholder="Title">
        </div>
        <div class="field">
          <label>Image</label>
          <input name="image" type="file" @change="onFileChanged">
        </div>
        <h5 v-if="uploading">Working on it</h5>
        <button type="submit" :disabled="uploading" :class="{ btnDisabled: uploading }">Post</button>
      </form>
    </div>
  </div>
</template>

<script>
const axios = require("axios");

// const CLOUDINARY_URL="https://api.cloudinary.com/v1_1/starscream97"
// const CLOUDINARY_UPLOAD_PRESET="rpk9lbbq"

export default {
  data() {
    return {
      image: null,
      title: "",
      uploading:false,
      CLOUDINARY_URL:"https://api.cloudinary.com/v1_1/starscream97/upload",
      CLOUDINARY_UPLOAD_PRESET:"rpk9lbbq",
      uploaded_image_url:"",
    };
  },
  
  methods: {
    onFileChanged(event) {
      this.image = event.target.files[0];
    },
    async createPost() {
      if(this.title && this.image){
        this.uploading=true;
        let formData=new FormData();
        formData.append('file',this.image);
        formData.append('upload_preset',this.CLOUDINARY_UPLOAD_PRESET)
        const savedImage=await axios({
          url: 'https://api.cloudinary.com/v1_1/starscream97/upload',
          method: 'POST',
          headers: {
              'Content-Type': 'application/x-www-form-urlencoded'
          },
          data: formData
        })
        this.uploaded_image_url=savedImage.data.url;
        this.uploading=false;


        const userId = JSON.parse(localStorage.getItem("user")).userId;  
        const result=await axios.post(`${process.env.VUE_APP_ROOT_API}api/post`,{
            title:this.title,
            user:userId,
            pictureURL:this.uploaded_image_url
        })
        // this.$socket.emit('newPost',result.data.data);
        this.$router.replace('/');
      }
        
    }
      
  }
};
</script>

<style scoped>
</style>


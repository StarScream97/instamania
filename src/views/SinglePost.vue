<template>
    <div class="singlePost" v-if="isLoaded">

      <div class="post-image">
          <img :src="post.pictureURL" alt="">
      </div>
      <div class="post-details">
        <div class="post-author">
            <h4>{{post.user.name}}</h4>
            <p>@{{post.user.username}}</p>
        </div>
        <div class="post-description">
          <h5 class="title">{{post.title}}</h5>
          <!-- <p>{{post.description}}</p> -->
          <h6>Likes: {{post.likes}}</h6>
        </div>
        
        <div class="comments">
          <div class="comment" v-for="comment in post.comments" :key="comment._id">
            <div class="comment-details">
              <h5>{{comment.commenterName}} </h5>
              <p>{{comment.comment.slice(0,40)}}</p>
            </div>
            <!-- <div class="comment-likes"> -->
              <!-- <span>Likes{{comment.likes}}</span> -->
              <!-- <i class="fas fa-heart"></i><span>{{comment.likes}}</span> -->
            <!-- </div> -->
          </div>
        </div>
        <div class="comment-form">
            <form action="" @submit.prevent="commentOnPost()">
              <input type="text" placeholder="Add a comment" v-model="newcomment">
            </form>
        </div>
      </div>
    </div>
</template>
<script>
import axios from 'axios';
export default {
    data(){
        return{
            postId:'',
            post:{},
            isLoaded:false,
            api_url:process.env.VUE_APP_ROOT_API,
            newcomment:'',
            user:{}
        }
    },
    computed:{
        fetchPost(){
            axios.get(`${process.env.VUE_APP_ROOT_API}api/post/${this.postId}`).then(res=>{
              this.post=res.data.data;
              this.isLoaded=true;
            })
        }
    },
    methods:{
      commentOnPost(){
          const postId=this.postId;
          const postUserId=this.post.user._id;
          const pictureURL=this.post.pictureURL
          axios.post(`${process.env.VUE_APP_ROOT_API}api/comment/comment`,{
            userId:this.user.userId,
            comment:this.newcomment,
            commenterName:this.user.name,
            postId
          }).then(res=>{
            this.newcomment=''
            this.post.comments.push(res.data.data);
          })
          this.$socket.emit('notification',{postId,comment:this.newcomment,notificationType:"Comment",to:postUserId,pictureURL,userId:this.user.userId,username:this.user.name});      
          this.$forceUpdate();
      }
    },
    created(){
        this.postId=this.$route.params.postId;
        this.fetchPost;
        const user=JSON.parse(localStorage.getItem('user'));
        if(user && user.userId!==''){
          this.user=user;
        }
    },
    beforeRouteUpdate(to){
        // this.postId=to.params.postId;
        // this.fetchPost;
    }
}
</script>

<style scoped>
  h4{
    font-size: 1.5rem;
  }
</style>


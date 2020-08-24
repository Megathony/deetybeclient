<template>
  <div class="home">
    <h1>Les Vidéos populaires du moment</h1>
    <div class="allVideos">
      <a class="video"  v-bind:key="video.id" v-for="video in responses" :id="video.id" :href="video.id">
        <img :src="video.snippet.thumbnails.default.url">
        <div class="text">
          <p class="title">{{video.snippet.title}}</p>
          <p class="channel">{{video.snippet.channelTitle}}</p>
        </div>
        <img class="button" src="../assets/app/addPlaylist.png">
      </a>
    </div>
  </div>
</template>

<script scr="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      responses: []
    };
  },
  mounted() {
    var self = this;
    axios
      .get("https://www.googleapis.com/youtube/v3/videos?part=snippet%2CcontentDetails%2Cstatistics&chart=mostPopular&maxResults=50&regionCode=FR&videoCategoryId=10&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY")
      .then( function(res){
        console.log('Data : ', res.data.items);
        self.responses = res.data.items;
      })
      .catch( function(error){
        console.log('Error : ', error);
      })
  },
  methods: {
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home h1{
  font-size: 30px;
}

.allVideos{
  margin-top: 50px;
  display: grid;
  grid-template-columns: repeat(3, 30%);
  grid-template-rows:  1fr;
  grid-column-gap: 5%;
  grid-row-gap: 30px; 
}

.video{
  position: relative;
  color:white;
  text-decoration:none;
  display: flex;
  background-color: rgb(40,40,40);
  padding: 10px;
  border-radius: 5px;
}

.video img{
  margin-right: 10px;
}

.video .text{
  display: block;
}

.video .text .title{
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  font-size: 15px;
  height: 30px;
}

.video .text .channel{
  color:rgb(200,200,200);
  text-decoration:none;
  margin-top: 10px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  font-size: 10px;
}

.video .button{
  position: absolute;
  width: 30px;
  right: 0;
  bottom: 10px;
}
</style>

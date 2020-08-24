<template>
  <div class="home">
    <h1>Les Vidéos populaires du moment</h1>
    <div class="wait" v-if="responses.length < 1">
      <h2>
          Recherche en cours...
      </h2>
    </div>
    <div v-else class="allVideos">
      <a class="video" v-bind:key="video.id" v-for="video in responses" :id="video.id" @click="say(video.id)">
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
    say: function(message) {
      console.log(message);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home h1{
  font-size: 30px;
}

.wait{
  text-align:center;
  height: calc(100vh - 170px);
}

.wait h2{
  position: relative;
  padding-top: calc(50vh - 170px);
  font-family: sans-serif;
  text-transform: uppercase;
  font-size: 2em;
  letter-spacing: 4px;
  overflow: hidden;
  background: linear-gradient(90deg, #1f2223, #fff, #1f2223);
  background-repeat: no-repeat;
  background-size: 80%;
  animation: animate 3s linear infinite;
  -webkit-background-clip: text;
  -webkit-text-fill-color: rgba(255, 255, 255, 0);
}

@keyframes animate {
  0% {
    background-position: -500%;
  }
  100% {
    background-position: 500%;
  }
}

.allVideos{
  margin-top: 50px;
  margin-bottom: 50px;
  display: grid;
  grid-template-columns: repeat(3, 30%);
  grid-template-rows:  1fr;
  grid-column-gap: 5%;
  grid-row-gap: 30px; 
}

.video{
  cursor: pointer;
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
  cursor: pointer;
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

.video .button:hover{
  width: 35px;
  right: -2.5px;
  bottom: 7.5px;
  transition-duration: 0.5s;
}
</style>

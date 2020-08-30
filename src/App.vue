<template>
    <div id="app">
      <header id="header">
      <i class="menu" @click="openMenu()">
        <img src="./assets/header/svg/menu.svg">
      </i>
      <div class="icon" href="../" @click="home()">
        <img src="./assets/header/svg/yt.svg">
        <h1 class="title"> DeeTube</h1>
      </div>
      <form class="searchBar" onsubmit="return false">
        <input type="text" v-model.lazy="search" v-on:change="searchVideo()" placeholder="Rechercher">
        <img class="submit" src="./assets/header/svg/search.svg">
      </form>
    </header>
    <section class="menu">
      <div :class="menu" class="leftBoard">
        <button @click="goPlaylist()">
          <img src="./assets/app/album.svg">
          vos playlists
        </button>
        <button @click="goCredit()">
          <img src="./assets/app/credit.svg">
          Crédit
        </button>

        <!-- Drogoni begin -->
        <button id="drogoniButton" @click="drogoniTest()">
          Start Drogoni's test...
        </button>
        <!-- Drogoni end -->
      </div>
    </section>
    <section class="scroll wrap">
      <div id="player"></div>
      <div id="content" :class="hideVideoPlayer">
        <div class="conteneurVideo" :class="showVideo">
          <div class="videoPlayer" v-if="videoIsPlaying == true">
            <img class="closeVideo" src="./assets/app/close.svg" @click="closeVideo()">
            <div id="ytplayer"></div>
            <iframe class="YTVideo" :src="videosrc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
            <img class="hideVideo" :class="hideVideoPlayer" src="./assets/app/up-chevron.svg" @click="hideVideo()">
          </div>
        </div>
        <div class="home" v-if="content == 'home'">
          <h1>Les Vidéos populaires du moment</h1>
          <div class="wait" v-if="mostPop.length < 1">
            <h2>
                Recherche en cours...
            </h2>
          </div>
          <div v-else class="allVideos">
            <div  class="video" v-bind:key="video.id" v-for="video in mostPop" :id="video.id" :title="video.snippet.title">
              <div class="selectVideo">
                <img :src="video.snippet.thumbnails.default.url" @click="playVideo(video.id)">
                <div class="text">
                  <p class="title" @click="playVideo(video.id)" >{{video.snippet.title}}</p>
                    <p class="channel" @click="exploreChannel(video.snippet.channelId , video.snippet.channelTitle)">{{video.snippet.channelTitle}}</p>
                </div>
              </div>
              <img class="button" src="./assets/app/addPlaylist.png" @click="addPlaylist(video)">
            </div>
          </div>
        </div>
        <div class="result" v-if="content == 'result'">
          <h1>Résultat pour votre recherche de {{search}}</h1>
          <div class="wait" v-if="resultsVideos.length < 1">
            <h2>
                Recherche en cours...
            </h2>
          </div>
          <div v-else>
            <div class="navButton">
              <p class="videos" @click="searchVideo()">
                Vidéos
              </p>
              <p class="channels" @click="searchChannel()">
                Channel
              </p>
              <p class="Playlists" @click="searchPlaylist()">
                Playlists
              </p>
            </div>
            <div v-if="searchNav == 'videos'" class="allVideos">
              <div  class="video" v-bind:key="video.id.videoId" v-for="video in resultsVideos" :title="video.snippet.title" >
                  <div class="selectVideo" >
                    <img :src="video.snippet.thumbnails.default.url" @click="playVideo(video.id.videoId)">
                    <div class="text">
                      <p class="title" @click="playVideo(video.id.videoId)">{{video.snippet.title}}</p>
                    <p class="channel" @click="exploreChannel(video.snippet.channelId , video.snippet.channelTitle)">{{video.snippet.channelTitle}}</p>
                    </div>
                  </div>
                  <img class="button" src="./assets/app/addPlaylist.png" @click="addPlaylist(video)">
              </div>
            </div>
            <div v-if="searchNav == 'channels'" class="allChannels">
              <div  class="channel" v-bind:key="channel.id.channelId" v-for="channel in resultsChannels" :title="channel.snippet.channelTitle" @click="exploreChannel(channel.snippet.channelId , channel.snippet.channelTitle)">
                  <div class="selectChannel">
                    <img :src="channel.snippet.thumbnails.default.url">
                    <p class="title">{{channel.snippet.channelTitle}}</p>
                  </div>
                </div>
              </div>
            <div v-if="searchNav == 'playlists'" class="allPlaylists">
              <div  class="searchPlaylist" v-bind:key="playlist.id.playlistId" v-for="playlist in resultsPlaylists" :title="playlist.snippet.playlistTitle" >
                  <div class="selectPlaylist" @click="explorePlaylist(playlist.id.playlistId)">
                    <img :src="playlist.snippet.thumbnails.default.url">
                    <div class="text">
                      <p class="title">{{playlist.snippet.title}}</p>
                      <p class="channel">{{playlist.snippet.channelTitle}}</p>
                    </div>
                  </div>
                </div>
              </div>
          </div>
        </div>
        <div class="channel" v-if="content == 'channel'">
          <img class="returnButton" @click="returnBackward()" src="./assets/app/returnArrow.png">
          <div class="wait" v-if="channelsVideos.length < 1">
            <h2>
                Chargement
            </h2>
          </div>
          <div v-else class="allChannelContent">
            <div class="channelInformations">
              <div class="title">
                <img :src="channelInformation.snippet.thumbnails.default.url">
                <h1>{{channelInformation.snippet.title}}</h1>
              </div>
              <p>{{channelInformation.snippet.description}}</p>
              <hr>
            </div>
            <div class="channelContent">
              <div class="ChannelVideos">
                <h1 class="Categorie">Videos :</h1>
                <div  class="ChannelVideo" v-bind:key="video.snippet.resourceId.videoId" v-for="video in channelsVideos" :id="video.snippet.resourceId.videoId" :title="video.snippet.title" >
                  <div class="selectVideo">
                    <img :src="video.snippet.thumbnails.default.url" @click="playVideo(video.snippet.resourceId.videoId)">
                      <div class="text">
                      <p class="title" @click="playVideo(video.snippet.resourceId.videoId)">{{video.snippet.title}}</p>
                    </div>
                  </div>
                  <img class="button" src="./assets/app/addPlaylist.png" @click="addPlaylist(video)">
                </div>
              </div>
              <div class="ChannelPlaylists">
                <h1 class="Categorie">Playlists :</h1>
                <div class="ChannelPlaylist" v-bind:key="playlist.id" v-for="playlist in channelsPlaylists" :id="playlist.id" :title="playlist.snippet.title" >
                  <div class="selectVideo">
                    <img :src="playlist.snippet.thumbnails.default.url" @click="explorePlaylist(playlist.id)">
                    <div class="text">
                      <p class="title" @click="explorePlaylist(playlist.id)">{{playlist.snippet.title}}</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="PlaylistContent" v-if="content == 'playlist'">
          <img class="returnButton" @click="returnBackward()" src="./assets/app/returnArrow.png">
          <div class="wait" v-if="playlistsVideos.length < 1">
            <h2>
                Chargement
            </h2>
          </div>
          <div v-else>
            <div class="optionPlaylist">
              <img src="./assets/app/addPlaylist.png">
              <img src="./assets/app/play-button.svg">
            </div>
            <div class="allVideos">
              <div  class="video" v-bind:key="video.id" v-for="video in playlistsVideos" :id="video.id" :title="video.snippet.title" >
                <div class="selectVideo">
                  <img :src="video.snippet.thumbnails.default.url" @click="playVideo(video.snippet.resourceId.videoId)">
                  <div class="text">
                    <p class="title" @click="playVideo(video.snippet.resourceId.videoId)">{{video.snippet.title}}</p>
                    <p class="channel">{{video.snippet.channelTitle}}</p>
                  </div>
                </div>
                <img class="button" src="./assets/app/addPlaylist.png" @click="addPlaylist(video)">
              </div>
            </div>
          </div>
        </div>
        <div class="PlaylistContent" v-if="content == 'credit'">
          <img class="returnButton" @click="returnBackward()" src="./assets/app/returnArrow.png">
          <h1>Crédits</h1>
        </div>
      </div>
    </section>
    <section class="player">
      <div class="button">
        <div class="nowPlaying" v-if="infoMusicPlaying != ''">
          <img :src="infoMusicPlaying.snippet.thumbnails.default.url">
          <div class="text">
            <p class="title">{{infoMusicPlaying.snippet.title}}</p>
            <p class="channel">{{infoMusicPlaying.snippet.channelTitle}}</p>
          </div>
        </div>
        <div class="nowPlaying" v-else>
          <img src="./assets/app/default.jpg">
          <div class="text">
            <p class="title">sélectionnez une musique à jouer</p>
            <p class="channel">do it</p>
          </div>
        </div>
        <div class="buttonCenter">
          <div class="buttonPlayer">
            <img v-if="musicPlayerRandom == true" @click="randomMode()" src="./assets/app/random-activate.svg">
            <img v-else @click="randomMode()" src="./assets/app/random.svg">
            <img src="./assets/app/previous.svg">
            <img @click="audioPlayerControl(1)" v-if="audioPlaying" src="./assets/app/audio-pause.svg">
            <img @click="audioPlayerControl(0)" v-else src="./assets/app/audio-play.svg">
            <img src="./assets/app/next.svg">
            <img v-if="musicPlayerRepeat == true" @click="repeatMode()" src="./assets/app/repeat-activate.svg">
            <img v-else @click="repeatMode()" src="./assets/app/repeat.svg">
          </div>
          <div class="timer">
            <p id="currentTime">00:52</p>
            <div class="avancementBar">
              <div id="progressBar" class="colorBar"></div>
            </div>
            <p id="durationTime">01:34</p>
          </div>
        </div>
        <div class="volume">
          <img src="./assets/app/speaker.svg">
          <div class="volumeBar">
            <div class="colorVolumeBar"></div>
          </div>
        </div>
        <img class="showPlaylist" :class="menuPlaylist" src="./assets/app/up-chevron.svg" @click="showPlaylist()">
      </div>
      <div class="playlist" :class="menuPlaylist">
        <div v-if="waitingList.length >= 1">
          <div  class="video" v-bind:key="video.id" v-for="video in waitingList" :id="video.id" :title="video.snippet.title">
            <div class="selectVideo" @click="playAudio(video)">
              <img :src="video.snippet.thumbnails.default.url">
              <div class="text">
                <p class="title" >{{video.snippet.title}}</p>
                <p class="channel">{{video.snippet.channelTitle}}</p>
              </div>
            </div>
            <img class="del" src="./assets/app/del.svg" @click="removeFromPlaylist(video.id)">
          </div>
        </div>
        <div v-else>
          <p class="emptyPlaylist">¯\_(ツ)_/¯<br>Sorry nothing here</p>
        </div>
      </div>
    </section>
    <footer>
    </footer>
    </div>
</template>

<!-- Compilation fix -->
<script src="https://www.youtube.com/iframe_api"></script>
<script>
import axios from "axios";

// Define constants
/**
 * Available player commands
 */
const PLAYER_COMMAND = {
  PLAY: 0,
  PAUSE: 1,
  NEXT: 2,
  PREVIOUS: 3
}

// Import Youtube IFrame API
var tag = document.createElement('script');
tag.src = "https://www.youtube.com/iframe_api";

var firstScriptTag = document.getElementsByTagName('script')[0];
firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

export default {
  name: "App",
  data() {
    return {
      mostPop: [],
      content: "home",
      search: "",
      searchNav:"videos",
      videosrc: "",
      resultsVideos: [],
      resultsChannels: [],
      resultsPlaylists: [],
      waitingList: [],
      channelInformation: "",
      channelsPlaylists: [],
      channelsVideos: [],
      playlistsVideos: [],
      lastAction: [],
      menuPlaylist: null,
      showVideo: "",
      hideVideoPlayer: "",
      videoIsPlaying: false,
      menu: null,
      musicPlayer: false,
      musicPlayerRandom: false,
      musicPlayerRepeat: false,
      // Music set to "Waterflame - Glorious Morning"
      musicPlaying: "7T_YtklLyyo",
      infoMusicPlaying:"",
      playlistMenu: false,

      // Drogni begin ~
      drogoniTestPlayer: undefined, // Player used
      drogoniTestLaunched: false,   // Environment is initialized
      drogoniTestPaused: false,      // Test is paused
      // ~ Drogoni end

      /**
       * Background player used to play audio.
       */
      audioPlayer: undefined,
      
      /**
       * Flag to notify if background player is initialized.
       * It will not notify if player is ready to play videos.
       */
      audioPlayerInit: false,

      /**
       * Flag to notify if background player is ready to play videos.
       */
      audioPlayerReady: false,

      /**
       * Flag to notify if a video is currently played.
       */
      audioPlaying: false
    };
  },
  mounted() {
    var self = this;
    if(self.mostPop.length < 1){
      axios
        .get("https://www.googleapis.com/youtube/v3/videos?part=snippet%2CcontentDetails%2Cstatistics&chart=mostPopular&maxResults=50&regionCode=FR&videoCategoryId=10&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY")
        .then( function(res){
          console.log('Data : ', res.data.items);
          self.mostPop = res.data.items;
        })
        .catch( function(error){
          console.log('Error : ', error);
        })
    }
  },
  methods: {
    home: function(){
      this.content = "home";
    },

    goPlaylist: function(){
      this.content = "yourPlaylist";
    },

    goCredit: function(){
      this.content = "credit";
    },

    playAudio: function(info){
      console.log("info playing now :", info);
      if(info.snippet.resourceId != undefined){
        this.musicPlaying = info.snippet.resourceId.videoId;
      } else {
        this.musicPlaying = info.id;
      }
      this.infoMusicPlaying = info;
    },

    hideVideo: function(){
      if(this.hideVideoPlayer == ""){
        this.hideVideoPlayer = "active";
      } else {
        this.hideVideoPlayer = "";
      }
    },

    closeVideo: function(){
      this.showVideo = "";
      this.videoIsPlaying = false;
    },

    openMenu: function(){
      if(this.menu == null){
        this.menu = "active";
      } else {
        this.menu = null;
      }
      console.log(this.menu);
    },

    say: function(message) {
      console.log(message);
    },

    addPlaylist: function(video) {
      this.waitingList.push(video) ;
    },

    removeFromPlaylist: function(id) {
      var tabTemp = [];
      for(var i = 0; i < this.waitingList.length ; i++){
        console.log(tabTemp);
        if(this.waitingList[i].id != id){
          tabTemp.push(this.waitingList[i]);
        }
      }
      this.waitingList = tabTemp;
      console.log(this.waitingList);
    },

    showPlaylist: function(){
      if(this.menuPlaylist == null){
        this.menuPlaylist = "show";
      } else {
        this.menuPlaylist = null;
      };
      console.log("showPlaylist :", this.menuPlaylist);
    },

    playVideo: function(id){
      this.videoIsPlaying = false;
      this.videosrc = "https://www.youtube.com/embed/" + id;
      this.showVideo = "active";
      this.hideVideoPlayer = "";
      this.videoIsPlaying = true;
      console.log(this.videosrc);
    },
    
    searchVideo: function(nextPage){
      var self = this;
      self.searchNav = "videos";
      if(self.search != ""){
        var url = "https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=50&type=video&q=" + self.search + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
        axios
          .get(url) //&pageToken=$page
          .then( function(res){
            console.log('Search Video : ', res.data.items);
            self.resultsVideos = res.data.items;
          })
          .catch( function(error){
            console.log('Error : ', error);
          })
        self.content = "result";
        console.log(self.search);
        console.log(self.resultsVideos);
      } else {
        self.content = "home";
      }
    },
    
    searchChannel: function(nextPage){
      var self = this;
      self.searchNav = "channels";
      if(self.search != ""){
        var url = "https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=50&type=channel&q=" + self.search + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
        axios
          .get(url) //&pageToken=" + nextPage + "
          .then( function(res){
            console.log('Search Channel : ', res.data.items);
            self.resultsChannels = res.data.items;
          })
          .catch( function(error){
            console.log('Error : ', error);
          })
        self.content = "result";
        console.log(self.search);
        console.log(self.resultsChannels);
      } else {
        self.content = "home";
      }
    },
    
    searchPlaylist: function(nextPage){
      var self = this;
      self.searchNav = "playlists";
      if(self.search != ""){
        if(self.resultsPlaylists.length <= 1 && nextPage == null){
          var url = "https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=50&type=playlist&q=" + self.search + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
          axios
            .get(url)
            .then( function(res){
              console.log('Search Playlist : ', res.data.items);
              self.resultsPlaylists = res.data.items;
            })
            .catch( function(error){
              console.log('Error : ', error);
            })
          self.content = "result";
          console.log(self.search);
          console.log(self.resultsPlaylists);
        } else {
          var searchTemp;
          var url = "https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=50&pageToken=" + nextPage + "&type=playlist&q=" + self.search + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
          axios
            .get(url)
            .then( function(res){
              console.log('Search Playlist : ', res.data.items);
              searchTemp = res.data.items;
            })
            .catch( function(error){
              console.log('Error : ', error);
            })
          self.content = "result";
          for(var i = 0; i <= searchTemp.length ; i++){
            self.resultsPlaylists.push(searchTemp[i]);
          }
          console.log(self.search);
          console.log(self.resultsPlaylists);
        }
      } else {
        self.content = "home";
      }
    },

    returnBackward: function(){
      this.content = this.lastAction[this.lastAction.length - 1];
      this.lastAction.length = this.lastAction.length - 1;
      console.log("content :" , this.content);
      console.log("lastAction :", this.lastAction);
    },

    exploreChannel: function(channelId , channelTitle){
      var self = this;
      self.lastAction.push(this.content);
      console.log("lastAction :", this.lastAction);
      console.log(channelId);
      self.content = "channel";
      console.log(self.content);
      var channelVideosId = "";
      var url = "https://www.googleapis.com/youtube/v3/channels?part=snippet%2CcontentDetails%2Cstatistics&id=" + channelId + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
        axios
          .get(url) //&pageToken=" + nextPage + "
          .then( function(res){
            console.log('Channel Information : ', res.data.items[0]);
            self.channelInformation = res.data.items[0];
            channelVideosId = self.channelInformation.contentDetails.relatedPlaylists.uploads;
            
          
            var urlVideo = "https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&maxResults=50&playlistId=" + channelVideosId + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
              axios
                .get(urlVideo) //&pageToken=" + nextPage + "
                .then( function(res){
                  console.log('Channel Videos : ', res.data.items);
                  self.channelsVideos = res.data.items;
                })
                .catch( function(error){
                  console.log('Error : ', error);
                })

          })
          .catch( function(error){
            console.log('Error : ', error);
          })
      
          
      var url = "https://www.googleapis.com/youtube/v3/playlists?part=snippet%2CcontentDetails&maxResults=50&channelId=" + channelId + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
        axios
          .get(url) //&pageToken=" + nextPage + "
          .then( function(res){
            console.log('Channel Playlists : ', res.data.items);
            self.channelsPlaylists = res.data.items;
          })
          .catch( function(error){
            console.log('Error : ', error);
          })
        console.log(self.channelsVideos);
        self.channelInformation = channelTitle;
    },

    explorePlaylist: function(playlistId){
      console.log(playlistId);
      var self = this;
      self.lastAction.push(this.content);
      console.log("lastAction :", this.lastAction);
      self.content = "playlist";
      console.log(self.content);
      var url = "https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&maxResults=50&playlistId=" + playlistId + "&key=AIzaSyC6YKZ7p684K-WnU7iLUgRlro9vtbGD-EY";
        axios
          .get(url) //&pageToken=" + nextPage + "
          .then( function(res){
            console.log('Playlist Informations : ', res.data.items);
            self.playlistsVideos = res.data.items;
          })
          .catch( function(error){
            console.log('Error : ', error);
          })
        console.log("test",self.playlistsVideos, self.content);
    },

    repeatMode: function(){
      this.musicPlayerRepeat = !this.musicPlayerRepeat;
      this.audioPlayer.setLoop(this.musicPlayerRepeat);
      console.log("Loop mode set to",this.musicPlayerRepeat);
    },

    // Function Random ~
    /**
      * When user click on random button, random mode is activated but if he was already activated, desactivate it.
      */

    randomMode: function(){
      if(this.musicPlayerRandom == false){
        this.musicPlayerRandom = true;
      } else {
        this.musicPlayerRandom = false;
      };
      console.log("Random :",this.musicPlayerRandom);
    },

    // Drogoni begin ~
    /**
      * Drogoni test function.
      *
      * It will control player test environment.
      * First click : Create environment
      * Second click: Pause video
      * Third click: Resume video
      */
    drogoniTest: function() {
      const DROGONI_BUTTON = document.getElementById("drogoniButton");

      // Setup test environment
      if (!self.drogoniTestLaunched)
      {
        alert("Starting Drogoni's test...");
        console.log("Creating player environment...");

        const body = document.body;
        var div = document.createElement("div");
        div.id = "drogoni_player";
        div.style.display = "none";
        body.appendChild(div);
        console.log("Player environment created.");

        // Assume YT API is ready
        const VIDEO_ID = this.musicPlaying;
        console.log('Selected video:', VIDEO_ID);
        self.drogoniTestPlayer = new YT.Player(div.id, {
          videoId: this.musicPlaying,
          events: {
            onReady: onDrogoniReady,
            onStateChange: onDrogoniChange,
            onError: onDrogoniError
          }
        });
        this.musicPlayer = true;

        self.drogoniTestLaunched = true;
        DROGONI_BUTTON.textContent = "Pause Drogoni's test";
        return;
      }

      // Toggle video playing
      if (!self.drogoniTestPaused)
      {
        this.musicPlayer = false;
        self.drogoniTestPlayer.pauseVideo();
        console.log("[Drogoni] Video paused");
        DROGONI_BUTTON.textContent = "Resume Drogoni's test";
      }
      else
      {
        this.musicPlayer = true;
        self.drogoniTestPlayer.playVideo();
        console.log("[Drogoni] Video resumed");
        DROGONI_BUTTON.textContent = "Pause Drogoni's test";
      }
      self.drogoniTestPaused = !self.drogoniTestPaused;
    },
    // ~ Drogoni end

    // -------------------------------------------
    // AUDIO BACKGROUND FUNCTIONS
    // -------------------------------------------
    
    /**
     * On background player ready.
     * 
     * When player is ready, it will begin to play videos and begin a task that will
     * update video progress information (time elapsed and fill progress bar).
     * 
     * Looped task is exectue every 0.5 seconds.
     * 
     * @param event - The event
     */
    onAudioReady: function(event)
    {
      console.log("Audio Player ready");
      this.audioPlayerReady = true;
      this.audioPlayer.loadPlaylist(this.musicPlaying);
      this.audioPlayer.playVideo();

      // Looped task 
      var self = this;
      setInterval(function() {
        if (self.audioPlayer.getPlayerState() == YT.PlayerState.PLAYING)
        {
          let currentTime = self.audioPlayer.getCurrentTime();
          let durationTime = self.audioPlayer.getDuration();

          // Update progress bar
          let percent = ((currentTime / durationTime) * 100).toFixed(0) + "%";
          document.getElementById("progressBar").style.width = percent;

          // Update time elapsed block
          let min = Math.floor(currentTime / 60);
          let sec = (currentTime % 60).toFixed(0);
          if (min < 10) min = "0" + min;
          if (sec < 10) sec = "0" + sec;
          document.getElementById("currentTime").textContent = min + ":" + sec;
        }
      }, 500);
    },

    /**
     * On background player state update.
     * 
     * Updating `audioPlaying` flag and if new statement is `BUFFERING`, video duration information
     * will be reflected on user interface.
     * 
     * @param event - The event
     */
    onAudioStateChange: function(event)
    {
      this.audioPlaying = (event.data == YT.PlayerState.PLAYING);
      
      if (event.data == YT.PlayerState.BUFFERING)
      {
        let duration = this.audioPlayer.getDuration();
        let min = Math.floor(duration / 60)
        let sec = (duration % 60).toFixed(0);
        if (min < 10) min = "0" + min;
        if (sec < 10) sec = "0" + sec;
        document.getElementById("durationTime").textContent = min + ":" + sec;

      }
    },

    /**
     * On background player.
     * 
     * @param event - The event
     */
    onAudioError: function(event)
    {
      // TODO Complete it.
    },

    /**
     * Initialize background player.
     * 
     * It will create an <div> at the end of <body> and contains the id `audioBackground`.
     * After be added, the background player is created and loaded on current `musicPlaying`.
     * 
     * The player will toggle `onAudioReady`, `onAudioStateChange` and `onAudioError` for player's events.
     * 
     * This function can be execute only one time.
     */
    audioPlayerSetup: function()
    {
      if (this.audioPlayerInit)
        return;
      
      console.log("Setup audio player...");
      
      // Create the place of audio player
      var playerFrame = document.createElement("div");
      playerFrame.id = "audioBackground";
      playerFrame.style.display = "none";
      document.body.appendChild(playerFrame);

      // Now setup player
      this.audioPlayer = new YT.Player(playerFrame.id, {
        events: {
          onReady: this.onAudioReady,
          onStateChange: this.onAudioStateChange,
          onError: this.onAudioError
        }
      });

      this.audioPlayerInit = true;
    },

    /**
     * Control the background player.
     * 
     * It will initialize the player if player isn't initialized and from passed command,
     * it will play video, pause video, etc..
     * 
     * If the audio player isn't ready, nothing will be done.
     * 
     * @param command - The command (please see at `PLAYER_COMMAND` constants for available commands)
     */
    audioPlayerControl: function(command)
    {
      this.audioPlayerSetup();
      if (!this.audioPlayerReady)
        return;
      
      console.debug("Audio Player command received:", command);

      switch (command)
      {
        case PLAYER_COMMAND.PLAY:
          this.audioPlayer.playVideo();
          break;
        case PLAYER_COMMAND.PAUSE:
          this.audioPlayer.pauseVideo();
          break;
        // TODO Add missing commands
        default:
          console.error("Command ID", command, "not implemented");
      }
    }
  }
};

// Drogoni begin ~
/**
  * When player is ready, let's start the video.
  */
function onDrogoniReady(event)
{
  console.log("[Drogoni] Drogoni test player ready, playing video...");
  event.target.playVideo();
}

/**
  * Listen to player's state updates.
  */
function onDrogoniChange(event)
{
  /*
  * State ID:
  *-1 = (Player not loaded)
  * 0 = YT.PlayerState.ENDED
  * 1 = YT.PlayerState.PLAYING
  * 2 = YT.PlayerState.PAUSED
  * 3 = YT.PlayerState.BUFERRING
  * 5 = YT.PlayerState.CUES
  */
  console.log("[Drogoni] Player state changed to", event.data);
  if (event.data == YT.PlayerState.ENDED)
  {
    self.musicPlayer = false;
    console.log("repeat ? : " ,self.musicPlayerRepeat);
    if(self.musicPlayerRepeat == true){
      event.target.playVideo();
    } else {
    alert("Video stopped");
    self.drogoniTestPaused = true;
    document.getElementById("drogoniButton").textContent = "Replay video";
    }
  }
}

/**
  * An error has occured ! Print error data to console.
  */
function onDrogoniError(event)
{
  console.error("[Drogoni] An error occured with player !");
  console.error(event);
}
// ~ Drogoni end
</script>

<style>

.icon{
  cursor: pointer;
}

.leftBoard{
    position: absolute;
    z-index: 3;
    width: 300px;
    left: -500px;
    height: calc(100vh - 140px);
    overflow-y: scroll;
    background-color: rgb(35, 38, 40);
    transition-duration: 0.7s;
}

.leftBoard button{
  display: flex;
  padding: 20px;
  font-size:20px;
  color: white;
  text-align: left;
  background:none;
  border: none;
  width:100%;
  height: 60px;
}

.leftBoard button img{
  width: 10%;
  padding-left: 10%;
  padding-right: 10%;
}

.leftBoard.active{
    transition-duration: 0.7s;
    left: 0px;
}

#content{
  transition-duration: 0.7s;
}

#content.active{
  margin-top: calc(-100vh + 70px + 120px);
  transition-duration: 0.7s;
}

.conteneurVideo{
  position: relative;
  height: 0px;
  transition-duration: 0.7s;
}

.conteneurVideo.active{
  height: calc(100vh - 200px);
  transition-duration: 0.7s;
  margin-bottom: 50px;
}

.YTVideo{
  margin-left:10%;
  width: 80%;
  height: 80%;
}

.closeVideo{
  transition-duration: 0.7s;
  position: absolute;
  right:20px;
  width: 40px;
}

.closeVideo:hover{
  transition-duration: 0.7s;
  transform: rotate(-180deg);
}

.hideVideo{
  position: absolute;
  bottom: 0;
  left:0;
  width: 40px;
  height:20px;
  margin-left:50%;
  transition-duration: 0.5s;
}

.hideVideo:hover{
  transition-duration: 1s;
  transform: rotate(360deg);
}

.hideVideo.active{
  transition-duration: 0.5s;
  transform: rotate(180deg);
}

.hideVideo.active:hover{
  transition-duration: 1s;
  transform: rotate(-180deg);
}

.videoPlayer{
  height: calc(100vh - 140px);
}

.scroll{
    overflow-y: scroll;
}

.wrap{
    height: calc(100vh - 100px - 140px);
    max-width: 70vw;
    padding:50px;
    background-color: rgb(31, 34, 35);
    margin:auto;
    box-shadow: 0px 0px 20px 5px rgba(0,0,0,0.75);
}

.home h1{
  font-size: 30px;
}

.wait{
  text-align:center;
  height: calc(100vh - 100px - 140px);
}

.wait h2{
  position: relative;
  padding-top: calc(50vh - 240px);
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

.allVideos .video{
  cursor: pointer;
  position: relative;
  color:white;
  text-decoration:none;
  background-color: rgb(40,40,40);
  padding: 10px;
  border-radius: 5px;
  height: 10vh;
}

.video .selectVideo{
  display: flex;
}

.video .selectVideo img{
  margin-right: 10px;
  cursor: pointer;
  height: 10vh;
}

.video .selectVideo .text{
  display: block;
}

.video .selectVideo .text .title{
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  font-size: 15px;
}

.video .selectVideo .text .channel{
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
  right: 5px;
  bottom: 10px;
}

.video .button:hover{
  width: 35px;
  right: 2.5px;
  bottom: 7.5px;
  transition-duration: 0.5s;
}

.allChannels{
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: repeat(5, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
} 

.allChannels .channel{
  margin:auto;
  margin-top:50px;
  padding: 10px;
  background-color:#282828;
  border-radius: 5px;
}

.allChannels .channel img{
  margin-left:50%;
  transform: translateX(-50%); 
}

.allPlaylists{    
  margin-top: 50px;
  margin-bottom: 50px;
  display: grid;
  grid-template-columns: repeat(3, 30%);
  grid-template-rows: 1fr;
  grid-column-gap: 5%;
  grid-row-gap: 30px;
}

.allPlaylists .searchPlaylist{    
  cursor: pointer;
  position: relative;
  color: white;
  text-decoration: none;
  background-color: rgb(40,40,40);
  padding: 10px;
  border-radius: 5px;
  height: 10vh;
}

.searchPlaylist .selectPlaylist{
  display: flex;
}

.searchPlaylist .selectPlaylist img{
  margin-right: 10px;
  cursor: pointer;
  height: 10vh;
}

.searchPlaylist .selectPlaylist .text{
  display: block;
}

.searchPlaylist .selectPlaylist .text .title{
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  font-size: 15px;
}

.searchPlaylist .selectPlaylist .text .channel{
  color:rgb(200,200,200);
  text-decoration:none;
  margin-top: 10px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  font-size: 10px;
}

.result .navButton{
  width: 100%;
  display:flex;
}

.result .navButton p{
  margin: auto;
  width: 30%;
  font-size: 20px;
  text-align: center;
  margin-top: 20px;
  padding-top: 20px;
  padding-bottom: 20px;
  transition-duration: 0.3s;
  border-color:#293432;
  border-radius:20px 20px 0px 0px;
  border-width: 1px;
  border-style: solid solid none solid;
}

.result .navButton p:hover{
  background-color:#293432;
  transition-duration: 0.3s;
}

.returnButton {
  width: 30px;
}

.allChannelContent{
  margin-top: 40px;
}

.allChannelContent .channelInformations p{
  margin-top:50px;
  margin-left: 50%;
  transform: translateX(-50%);
  text-align: center;
  width: 80%;
}

.allChannelContent .channelInformations hr{
  margin-top:50px;
  margin-bottom: 50px;
}

.allChannelContent .channelInformations .title{
  display: flex;
  margin: 20px;
  height: 100px;
  width: 100%;
}

.allChannelContent .channelInformations .title img{
  width: 100px;
}

.allChannelContent .channelInformations .title h1{
  margin-top:30px;
  margin-left: 20px;
  font-size: 40px;
}

.Categorie{
  margin-left: 10px;
  font-size: 20px;
}

.allChannelContent .channelContent{
  display: flex;
}

.allChannelContent .channelContent .ChannelVideos{
  width: 50%;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo{
  cursor: pointer;
  position: relative;
  color:white;
  text-decoration:none;
  background-color: rgb(40,40,40);
  margin: 10px;
  padding: 10px;
  border-radius: 5px;
  height: 10vh;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo .selectVideo{
  display: flex;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo  .selectVideo img{
  margin-right: 10px;
  cursor: pointer;
  height: 10vh;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo  .selectVideo .text{
  display: block;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo  .selectVideo .text .title{
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  font-size: 15px;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo .button{
  position: absolute;
  width: 30px;
  right: 5px;
  bottom: 10px;
}

.allChannelContent .channelContent .ChannelVideos .ChannelVideo .button:hover{
  width: 35px;
  right: 2.5px;
  bottom: 7.5px;
  transition-duration: 0.5s;
}

.allChannelContent .channelContent .ChannelPlaylists{
  width: 50%;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist{
  cursor: pointer;
  position: relative;
  color:white;
  text-decoration:none;
  background-color: rgb(40,40,40);
  margin: 10px;
  padding: 10px;
  border-radius: 5px;
  height: 10vh;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist .selectVideo{
  display: flex;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist  .selectVideo img{
  margin-right: 10px;
  cursor: pointer;
  height: 10vh;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist  .selectVideo .text{
  display: block;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist  .selectVideo .text .title{
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  font-size: 15px;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist .button{
  position: absolute;
  width: 30px;
  right: 5px;
  bottom: 10px;
}

.allChannelContent .channelContent .ChannelPlaylists .ChannelPlaylist .button:hover{
  width: 35px;
  right: 2.5px;
  bottom: 7.5px;
  transition-duration: 0.5s;
}

.PlaylistContent .optionPlaylist{
  display: flex;
}

.PlaylistContent .optionPlaylist img{
  width: 40px;
  height: 40px;
  margin: 10px;
}

.player{
  position: absolute;
  width: 100%;
  box-shadow: 0px 0px 20px 5px rgba(0,0,0,0.75);
}

.player .button{
  background-color: #780000;
  position: relative;
  display: flex;
  z-index: 8;
  width: 100%;
  height: 70px;
}

.player .button .nowPlaying{
  width:30%;
  display: flex;
}

.player .button .nowPlaying img{
  height: 70px;
  width:auto;
}

.player .button .nowPlaying .text .title{
  color: white;
  font-size: 15px;    
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.player .button .nowPlaying .text .channel{
  color: #AAAAAA;
  font-size: 10px;
}

.player .button .buttonCenter{
  width: 40%;
  display:block;
}

.player .button .buttonCenter .buttonPlayer{
  display: flex;
  justify-content: space-between;
}

.player .button .buttonCenter .buttonPlayer img{
  align-items:center;
  margin: auto;
  margin-top: 10px;
  width: 30px;
  transition-duration: 0.7s;
}

.player .button .buttonCenter .timer{
  display: flex;
  margin-left: 50%;
  transform: translateX(-50%);
  width: 70%;
  margin-top: 10px;
}

.player .button .buttonCenter .timer p{
  transform: translateY(-50%);
  text-align: center;
  width: 15%;
  height: 10px;
  font-size: 20px;
}

.player .button .buttonCenter .timer .avancementBar{
  width: 70%;
  height: 10px;
  border-radius: 4px;
  border-style: solid;
  border-width: 1px;
  border-color: #1f2223;
  box-shadow: 0px 0px 20px 5px rgba(0,0,0,0.30);
}

.player .button .buttonCenter .timer .avancementBar .colorBar{
  border-radius: 4px;
  background-color: #999999;
  height: 10px;
  width: 66%;
}

.player .button .volume{
  display: flex;
  width: 30%;
  height: 70px;
  padding-left: 50px;
}

.player .button .volume img{
  width: 30px;
  margin-right: 20px;
}

.player .button .volume .volumeBar{
  margin-top: 30px;
  width: 30%;
  height: 10px;
  border-radius: 4px;
  border-style: solid;
  border-width: 1px;
  border-color: #1f2223;
  box-shadow: 0px 0px 20px 5px rgba(0,0,0,0.30);
}

.player .button .volume .colorVolumeBar{
  border-radius: 4px;
  background-color: #999999;
  height: 10px;
  width: 66%;
}

.player .button .showPlaylist{
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 30px;
  right: 10px;
  transition-duration: 0.7s;
}

.player .button .showPlaylist.show{
  transform:  translateY(-50%) rotate(180deg);
  transition-duration: 0.7s;
}

.emptyPlaylist{
  margin-top: calc(50vh - 140px);
  transform:  translateY(-50%);
  opacity: 30%;
  text-align:center;
  font-size: 5vh;
}

.playlist{
  position: relative;
  max-width: 70vw;
  margin: auto;
  padding: 50px;
  background-color: rgb(0,0,0);
  z-index: 5;
  height: calc(100vh - 70px - 70px - 100px);
  top: calc(100vh - 70px - 70px);
  overflow-y:scroll;
  transition-duration: 0.7s;
}

.playlist.show{
  background-color: #670000;
  top: calc(-100vh + 70px);
  transition-duration: 0.7s;
}

.playlist .video{
  background-color: #870000;
  position: relative;
  margin-bottom:10px;
  padding:5px;
  border-radius: 5px;
  box-shadow: 0px 0px 20px 5px rgba(0,0,0,0.10);
}

.playlist .video .del{
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  right: 50px;
  width: 20px;
}
</style>

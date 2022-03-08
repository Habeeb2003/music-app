<template>
  <main class="">
    <div style="height: 10%">
      <h4 class="text-center py-1" style="border-bottom: 2px solid grey">Play</h4>
    </div>
    <div class="text-center" style="height: 20%; display: grid; place-items: center;">
      <h5 class="mb-0" id="nowPlayingTitle">{{(currentSong == "") ? "Song Title" : currentSong.title}}</h5>
      <h6 id="nowPlayingSinger">{{(currentSong == "") ? "artist" : currentSong.artist}}</h6>
    </div>
    <div class="d-flex justify-content-center align-items-center" style="height: 40%">
      <img :src="currentSong.picture" style="width: 150px; height: 150px; border-radius: 100%; border: 2px solid grey" alt="">
    </div>
    <!-- <audio id="audio" >
      <source src="../public/music/Ruger_Bounce.mp3" type="audio/mpeg">
    </audio> -->
    <audio id="audio" :src="(currentSong.songData)"></audio>
    <div class="d-flex justify-content-evenly align-items-center" style="height: 10%">
      <span>{{currentSongPlayTime}}</span>
      <input type="range" @input="seek" min="0.00" :max="curSongDurInSecs" v-model="progressValue"  name=""  id="" style="width: 65%">
      <span>{{currentSongDuration}}</span>
    </div>
    <div class="controlBtnDiv d-flex justify-content-evenly" style="height: 20%">
      <button id="shuffleBtn" @click="() => pickRandomSong('clicked')"><i id="shuffleBtnIcon" class="fa fa-random" aria-hidden="true"></i></button>
      <button id="previousBtn" @click="playPreviousSong"><i class="fa fa-step-backward" aria-hidden="true"></i></button>
      <button v-if="!isPlaying" @click="play" id="playBtn"><i class="fa fa-play-circle" aria-hidden="true"></i></button>
      <button v-if="isPlaying" @click="pause" id="pauseBtn"><i class="fa fa-pause-circle" aria-hidden="true"></i></button>
      <button id="nextBtn" @click="playNextSong"><i class="fa fa-step-forward" aria-hidden="true"></i></button>
      <button id="playlistBtn"><i class="iconify" data-icon="bxs:playlist"></i></button>
    </div>
  </main>
  <main>
    <div style="height: 10%">
      <h4 class="text-center py-1" style="border-bottom: 2px solid grey">SONGS</h4>
    </div>
    <div class="songList">
      <h4 class="text-center" v-if="songs.length == 0">No Song In Playlist</h4>
      <div v-for="(song, index) in songs" :key="index" class="my-1 so_ng p-2 d-flex justify-content-evenly align-items-center">
        <button @click="() => playThisSong(song)" class="d-flex justify-content-center align-item-center" style="width: 90%; background-color: transparent;">
          <div class="bg-primary" style="width: 50px; height: 50px; border-radius: 100%;">
            <img :src="(song.picture != 'Unknown') ? song.picture : '' " style="width: 50px; height: 50px;" alt="">
          </div>
          <div style="width: 65%; text-align: left; padding-left: 10px" class="">
            <p class="m-0" style="font-size: 17px">{{song.title.slice(0,15)}}...</p>
            <p class="m-0" style="font-size: 14px">{{song.artist.slice(0,15)}}...</p>
          </div>
        </button>
        <button @click="() => removeSongFromPlaylist(song)" style="width: 5%">
          <i  class="fa fa-ellipsis-v"></i>
        </button>
      </div>
      
    </div>
    <div  style="height: 10%;">
      <button @click="addSongsToPlaylist" style="background-color: lightblue; border: none; width: 100%; height: 100%;">CLICK TO ADD SONG TO PLAYLIST <input type="file" @change="readMusicMetadata" multiple name="" id="musicFileSelector" hidden></button>
    </div>
  </main>
  <!-- <audio controls >
    <source src="../public/music/Billie Eilish - Lost Cause (Official Music Video)_160k.mp3" type="">
  </audio> -->
    <!-- <input type="file" @change="readMusicMetadata" name="" id="musicFile"> -->
  <router-view/>
</template>

<script>
// import './assets/dist/jsmediatags.min.js'
import 'bootstrap/dist/css/bootstrap.min.css'
// import 'jquery/src/jquery.js'
// import 'bootstrap/dist/js/bootstrap.min.js'

export default {
  data(){
    return{
      currentSong: "",
      songs: [
      ],
      progressValue: 0,
      curSongDurInSecs: 0,
      currentSongDuration: "00:00",
      currentSongPlayTime: "00:00",
      songIndex: 0,
      isPlaying: false,
      loadSongData: false,
      isRandom: false,
    }
  },
  methods:{
    init(){
      
    },
    loadSong(index){
      this.currentSong = this.songs[index]
      console.log(this.songs[index]);
      document.getElementById('audio').src = this.currentSong.songData
      document.getElementById('audio').addEventListener('timeupdate', () => {
        this.updateSlider()
        this.setCurrentPlayTime()
      })
      document.getElementById('audio').addEventListener('loadeddata', async () => {
        this.setCurrentSongDuration()
        this.loadSongData = true
        this.curSongDurInSecs = document.querySelector('audio').duration
        if (this.isPlaying = true) {
          document.getElementById('audio').play()
        }
      })
      document.getElementById('audio').addEventListener('ended', this.playNextSong)
    },
    readMusicMetadata(){
      const jsmediatags = window.jsmediatags
      console.log(jsmediatags);
      const files = document.getElementById('musicFileSelector').files
      for (let i = 0; i < files.length; i++) {
        const fileReader = new FileReader()
        jsmediatags.read(files[i], {
          onSuccess: (result) => {
            console.log(result);
            let rs = 'title' in result.tags
            let newSong = {title: "", artist: "", album: "", picture: "", genre: "", year: "", songData: ""}
            console.log(rs)
            if('picture' in result.tags){
              const { data, format } = result.tags.picture;
              let base64String = "";
              for (let i = 0; i < data.length; i++) {
                base64String += String.fromCharCode(data[i]);
              }
              newSong.picture = `data:${data.format};base64,${window.btoa(base64String)}`;
            }
            else{
              newSong.picture = "Unknown"
            }
            newSong.title = ('title' in result.tags) ? result.tags.title : files[i].name;
            newSong.artist = ('artist' in result.tags) ? result.tags.artist : "Unknown";
            newSong.album = ('album' in result.tags) ? result.tags.album : "Unknown";
            newSong.genre = ('genre' in result.tags) ? result.tags.genre : "Unknown";
            newSong.year = ('year' in result.tags) ? result.tags.year : "Unknown";
            fileReader.readAsDataURL(files[i])
            fileReader.onload = () => {
              newSong.songData = fileReader.result
              this.songs.push(newSong)
              console.log('done')
            }
          },
          onError:  (error) => {
            console.log(error);
          }
        })
      }
    },
    addSongsToPlaylist(){
      document.getElementById('musicFileSelector').click()
    },
    playThisSong(song){
      if (this.currentSong == "") {
        this.loadSong(this.songs.indexOf(song))
        this.songIndex = this.songs.indexOf(song)
        this.isPlaying = true
        return
      }
      this.currentSong = song
      this.songIndex = this.songs.indexOf(song)
      // document.getElementById('audio').src = song.songData
      this.isPlaying = true
      console.log(song);
    },
    play(){
      if (this.currentSong == "") {
        this.loadSong(this.songIndex)
        this.isPlaying = true
        return
      }
      document.getElementById('audio').play()
      this.isPlaying = true
    },
    pause(){
      document.getElementById('audio').pause()
      this.isPlaying = false
    },
    updateSlider(){
      this.progressValue = document.getElementById('audio').currentTime
    },
    seek(){
      document.getElementById('audio').currentTime = this.progressValue
    },
    setCurrentSongDuration(){
      let minutes = (Math.floor(document.getElementById('audio').duration / 60)).toString().padStart(2,'0');
      let seconds = (Math.floor(document.getElementById('audio').duration - minutes * 60)).toString().padStart(2,'0');
      this.currentSongDuration = minutes + ":" + seconds
    },
    setCurrentPlayTime(){
      let minutes = (Math.floor(document.getElementById('audio').currentTime / 60)).toString().padStart(2,'0');
      let seconds = (Math.floor(document.getElementById('audio').currentTime - minutes * 60)).toString().padStart(2,'0');
      this.currentSongPlayTime = minutes + ":" + seconds
    },
    playNextSong(){
      if (this.isRandom == true) {
        this.pickRandomSong()
        return
      }
      this.songIndex ++
      if (this.songIndex == (this.songs.length)) {
        this.songIndex = 0
      }
      this.currentSong = this.songs[this.songIndex]
      document.getElementById('audio').src = this.currentSong.songData
    },
    playPreviousSong(){
      this.songIndex --
      if (this.songIndex == -1) {
        this.songIndex = (this.songs.length -1)
      }
      this.currentSong = this.songs[this.songIndex]
      document.getElementById('audio').src = this.currentSong.songData
    },
    removeSongFromPlaylist(song){
      this.songs.splice(this.songs.indexOf(song), 1)
      console.log("song menu");
    },
    pickRandomSong(clicked){
      if(this.currentSong == "")
        return
      if (clicked == "clicked") {
        this.isRandom = !this.isRandom;
        console.log(this.isRandom);
        // this.songIndex = this.songs.indexOf(this.currentSong)
        (this.isRandom == true) ? document.getElementById('shuffleBtnIcon').style.color = "black" : document.getElementById('shuffleBtnIcon').style.color = "gray"
      }
      if (this.isRandom == false) {
        this.songIndex = this.songs.indexOf(this.currentSong)
        return
      }
      if (this.isRandom == true && this.isPlaying == true && clicked == 'clicked') {
        return
      }
      let randNum = Math.floor(Math.random() * this.songs.length)
      this.currentSong = this.songs[randNum]
      this.songIndex = this.songs.indexOf(this.currentSong)
    }
  },
  mounted(){
  }
}
</script>
<style lang="scss">
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  margin: 0;
  padding: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  #app{
    width: 100%;
    display: flex;
    justify-content: space-around;
  }
  main{
    width: 303px;
    height: 500px;
    background: #F1F1F1;
    box-shadow: 0px 0px 10px 10px rgba(0, 0, 0, 0.5);
    border-radius: 40px;
    margin: auto;
    overflow: hidden;
    .controlBtnDiv{
      align-items: center;
      button{
        border: none;
      }
      #playBtn i, #pauseBtn i{
        font-size: 45px
      }
      #previousBtn i, #nextBtn i, #shuffleBtn i, #playlistBtn svg{
        font-size: 30px;
      }
      #shuffleBtn i{
        color: gray
      }
    }
    .songList{
      min-height: 80%;
      max-height: 80%;
      overflow-y: scroll;
      button:nth-child(1){
        width: 100%;
        border: none;
        
      }
      button:last-child {
          border: none;
          background-color: transparent;
      }
      
    }
    .so_ng{
      background-color: lightgray
    }
  }
}
</style>

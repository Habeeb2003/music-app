<template>
  <main class="">
    <div style="height: 10%">
      <h4 class="text-center py-1" style="border-bottom: 2px solid grey">Play</h4>
    </div>
    <div class="text-center" style="height: 20%; display: grid; place-items: center;">
      <h5 class="mb-0" id="nowPlayingTitle">Dancing In My Room</h5>
      <h6 id="nowPlayingSinger">347aidan</h6>
    </div>
    <div class="text-center" style="height: 40%">
      <img src="" style="width: 150px; height: 150px; border-radius: 100%; border: 2px solid grey" alt="">
    </div>
    <!-- <audio id="audio" >
      <source src="../public/music/Ruger_Bounce.mp3" type="audio/mpeg">
    </audio> -->
    <div class="d-flex justify-content-evenly align-items-center" style="height: 10%">
      <span>00.00</span>
      <input type="range"  name=""  id="">
      <span>03.20</span>
    </div>
    <div class="controlBtnDiv d-flex justify-content-evenly" style="height: 20%">
      <button id="shuffleBtn"><i class="fa fa-random" aria-hidden="true"></i></button>
      <button id="previousBtn"><i class="fa fa-step-backward" aria-hidden="true"></i></button>
      <button @click="play" id="playBtn"><i class="fa fa-play-circle" aria-hidden="true"></i></button>
      <button hidden id="pauseBtn"><i class="fa fa-pause-circle" aria-hidden="true"></i></button>
      <button id="nextBtn"><i class="fa fa-step-forward" aria-hidden="true"></i></button>
      <button id="playlistBtn"><i class="iconify" data-icon="bxs:playlist"></i></button>
    </div>
  </main>
  <main>
    <div style="height: 10%">
      <h4 class="text-center py-1" style="border-bottom: 2px solid grey">SONGS</h4>
    </div>
    <div class="songList">
      <div v-for="(song, index) in songs" :key="index" class="my-1 so_ng p-2 d-flex justify-content-evenly align-items-center">
        <div class="bg-primary" style="width: 50px; height: 50px; border-radius: 100%;">
          <img :src="(song.picture != 'Unknown') ? song.picture : '' " style="width: 100%" alt="">
        </div>
        <div>
          <p class="m-0" style="font-size: 17px">{{song.title}}</p>
          <p class="m-0" style="font-size: 14px">{{song.artist}}</p>
        </div>
      </div>
      
    </div>
    <div  style="height: 10%;">
      <button @click="addSongsToPlaylist" style="background-color: lightblue; border: none; width: 100%; height: 100%;">CLICK TO ADD SONG TO PLAYLIST <input type="file" @change="readMusicMetadata" name="" id="musicFileSelector" hidden></button>
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
      isPlaying: false,
    }
  },
  methods:{
    init(){
      
    },
    readMusicMetadata(){
      const jsmediatags = window.jsmediatags
      console.log(jsmediatags);
      const file = document.getElementById('musicFileSelector').files[0]
      const fileReader = new FileReader()
      jsmediatags.read(file, {
        onSuccess: (result) => {
          console.log(result);
          let rs = 'title' in result.tags
          console.log(rs)
          const { data, format } = result.tags.picture;
          let base64String = "";
          for (let i = 0; i < data.length; i++) {
            base64String += String.fromCharCode(data[i]);
          }
          this.src = `data:${data.format};base64,${window.btoa(base64String)}`;
          let newSong = {title: "", artist: "", album: "", picture: "", genre: "", year: "", songData: ""}
          newSong.title = ('title' in result.tags) ? result.tags.title : "Unknown";
          newSong.artist = ('artist' in result.tags) ? result.tags.artist : "Unknown";
          newSong.album = ('album' in result.tags) ? result.tags.album : "Unknown";
          newSong.picture = ('picture' in result.tags) ? result.tags.picture : "Unknown";
          newSong.genre = ('genre' in result.tags) ? result.tags.genre : "Unknown";
          newSong.year = ('year' in result.tags) ? result.tags.year : "Unknown";
          fileReader.readAsDataURL(file)
          fileReader.onload = () => {
            newSong.songData = fileReader.result
            this.songs.push(newSong)
          }
        },
        onError:  (error) => {
          console.log(error);
        }
      })
    },
    addSongsToPlaylist(){
      document.getElementById('musicFileSelector').click()
    },
    play(){
      //let audioObj = new Audio("../public/music/Ruger_Bounce.mp3")
      // audioObj.addEventListener("canplay", (event) => {
      //   console.log(event);
      // })
      let aud = document.getElementById('audio')
      // aud.onloadedmetadata = (mess) => {
      //   console.log('tele');
      //   console.log(mess);
      // }
      aud.play()
      // console.log(audioObj);
    },
    pause(){
      document.getElementById('audio').pause()
    },
    playNextSong(){

    },
    playPreviousSong(){

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
      #playBtn i{
        font-size: 45px
      }
      #previousBtn i, #nextBtn i, #shuffleBtn i, #playlistBtn svg{
        font-size: 30px;
      }
    }
    .songList{
      max-height: 80%;
      overflow-y: scroll;
    }
    .so_ng{
      background-color: lightgray
    }
  }
}
</style>

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
    <div class="d-flex justify-content-evenly align-items-center" style="height: 10%">
      <span>00.00</span>
      <input type="range"  name=""  id="">
      <span>03.20</span>
    </div>
    <div class="controlBtnDiv d-flex justify-content-evenly" style="height: 20%">
      <button id="shuffleBtn"><i class="fa fa-random" aria-hidden="true"></i></button>
      <button id="previousBtn"><i class="fa fa-step-backward" aria-hidden="true"></i></button>
      <button id="playBtn"><i class="fa fa-play-circle" aria-hidden="true"></i></button>
      <button hidden id="pauseBtn"><i class="fa fa-pause-circle" aria-hidden="true"></i></button>
      <button id="nextBtn"><i class="fa fa-step-forward" aria-hidden="true"></i></button>
      <button id="playlistBtn"><i class="iconify" data-icon="bxs:playlist"></i></button>
    </div>
  </main>
  <router-view/>
</template>
<script>

import 'bootstrap/dist/css/bootstrap.min.css'
// import 'jquery/src/jquery.js'
// import 'bootstrap/dist/js/bootstrap.min.js'

export default {
  
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
  main{
    width: 303px;
    height: 500px;
    background: #F1F1F1;
    box-shadow: 0px 0px 10px 10px rgba(0, 0, 0, 0.5);
    border-radius: 40px;
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
  }
}
</style>

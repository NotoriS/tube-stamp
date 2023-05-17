<script>
import axios from "axios"
import Header from "./Header.vue"
import Footer from "./Footer.vue"

export default {
  components: {
    Header,
    Footer
  },
  data() {
    return {
      URL: '',
      inputValidity: ''
    }
  },
  methods: {
    async getVideoData() {
      var videoID = this.youtubeParser(this.URL)
      if (videoID) {
        var apiData = await axios.get("https://youtube.googleapis.com/youtube/v3/videos?part=snippet&id=" + videoID + "&key=AIzaSyCn0Z4eeX3JhZqcjjpiODD1Xx4AYrcTv0s")
        if (apiData.data.items.length == 1) {
          this.inputValidity = ''
          var videoData = apiData.data.items[0].snippet
          console.log(videoData)
        } else {
          this.inputValidity = 'is-invalid'
          this.URL = ''
        }
      } else {
        this.inputValidity = 'is-invalid'
        this.URL = ''
      }
    },
    // Function modified from: https://stackoverflow.com/a/8260383
    youtubeParser(url) { 
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(shorts\/)|(watch\?))\??v?=?([^#&?]*).*/
      var match = url.match(regExp)
      return (match&&match[8].length==11)? match[8] : false
    }
  }
}
</script>

<template>
  <Header />
  <main>
    <div class="container">
      <div class=" container bg-light mt-5 p-5 border">
        <h2 class="text-muted text-center mb-4">Find a YouTube Upload Date</h2>
        <form class="d-flex flex-row mx-auto" @submit.prevent="getVideoData">
          <input v-model="URL" type="text" class="form-control input-margin shadow-none" :class="inputValidity" placeholder="Enter a YouTube URL">
          <button type="submit" class="btn btn-dark shadow-none">Enter</button>
        </form>
        <div v-if="inputValidity == 'is-invalid'" class="warning-block p-3 mt-5">
          <p class="text-center m-auto">The URL you have entered is invalid.</p>
        </div>
      </div>
    </div>
  </main>
  <Footer />
</template>

<style type="text/css">
.warning-block {
  background-color: #F2DEDE;
}
.warning-block > p {
  color: #A94442;
}
.input-margin {
  margin-right: 10px;
}
</style>
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
      this.URL = ''
      if (videoID) {
        var apiData = await axios.get("https://youtube.googleapis.com/youtube/v3/videos?part=snippet&id=" + videoID + "&key=AIzaSyCn0Z4eeX3JhZqcjjpiODD1Xx4AYrcTv0s")
        if (apiData.data.items.length == 1) {
          this.inputValidity = ''
          var videoData = apiData.data.items[0].snippet
          console.log(videoData)
        } else {
          this.inputValidity = 'is-invalid'
        }
      } else {
        this.inputValidity = 'is-invalid'
      }
    },
    // Function from: https://stackoverflow.com/a/8260383
    youtubeParser(url) { 
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#&?]*).*/
      var match = url.match(regExp)
      return (match&&match[7].length==11)? match[7] : false
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
          <input v-model="URL" type="text" class="form-control" :class="inputValidity" style="margin-right: 10px" placeholder="Enter a YouTube URL">
          <button type="submit" class="btn btn-dark">Enter</button>
        </form>
      </div>
    </div>
  </main>
  <Footer />
</template>

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
      inputValidity: '',
      videoThumbnail: '',
      videoTitle: '',
      videoPublishDate: ''
    }
  },
  methods: {
    async getVideoData() {
      var videoID = this.youtubeParser(this.URL)
      if (videoID) {
        var apiData = await axios.get("https://youtube.googleapis.com/youtube/v3/videos?part=snippet&id=" + videoID + "&key=AIzaSyCn0Z4eeX3JhZqcjjpiODD1Xx4AYrcTv0s")
        if (apiData.data.items.length == 1) {
          this.inputValidity = 'is-valid'
          var videoData = apiData.data.items[0].snippet
          console.log(videoData)
          this.videoThumbnail = videoData.thumbnails.maxres.url
          this.videoTitle = videoData.title
          this.videoPublishDate = videoData.publishedAt
          console.log(this.videoPublishDate)
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
      <div class=" container bg-light my-5 p-5 border">
        <h2 class="text-muted text-center mb-4">Find a YouTube Publish Date</h2>
        <form class="d-flex flex-row mx-auto" @submit.prevent="getVideoData">
          <input v-model="URL" type="text" class="form-control input-margin shadow-none" :class="inputValidity" placeholder="Paste a YouTube URL">
          <button type="submit" class="btn btn-dark shadow-none">Enter</button>
        </form>
        <div v-if="inputValidity == 'is-invalid'" class="warning-block p-3 mt-5">
          <p class="text-center m-auto">The URL you have entered is invalid.</p>
        </div>
        <div v-if="inputValidity == 'is-valid'" class="row mt-5">
          <div class="col-lg mb-5">
            <img class="img-fluid mb-2" :src="videoThumbnail">
            <div class="text-center">
              <b>
                {{ videoTitle }}
              </b>
            </div>
          </div>
          <div class="col-lg text-center my-auto">
            <h3><b>Date and Time Published</b></h3>
            <div class="my-5">January 15th 12:00pm</div> <!-- Placeholder -->
            <h3><b>Time Elapsed Since Publishing</b></h3>
            <div class="my-5">10 Days 14 Hours 15 Minutes 12 Seconds</div> <!-- Placeholder -->
          </div>
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
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
      videoPublishDate: '',
      videoPublishDateString: '',
      timeElapsedSincePublish: ''
    }
  },
  methods: {
    // Function modified from: https://stackoverflow.com/a/8260383
    youtubeParser(url) { 
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(shorts\/)|(watch\?))\??v?=?([^#&?]*).*/
      var match = url.match(regExp)
      return (match&&match[8].length==11)? match[8] : false
    },
    setVideoPublishDateString(publishDate) {
      const dayNames = ["Sunday", "Monday", "Tuesday", "Wednesday","Thursday", "Friday", "Saturday"]
      const day = dayNames[publishDate.getDay()]

      const monthNames = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"]
      const month = monthNames[publishDate.getMonth()]

      const dayOfMonth = publishDate.getDate()
      const year = publishDate.getFullYear()

      var hours = publishDate.getHours()
      const ampm = (hours < 12)? "a.m." : "p.m."
      hours %= 12
      if (hours == 0) 
        hours = 12

      var minutes = publishDate.getMinutes()
      if (minutes < 10)
        minutes = "0" + minutes

      this.videoPublishDateString = day + ", " + month + " " + dayOfMonth + ", " + year + ", at " + hours + ":" + minutes + " " + ampm
    },
    setTimeElapsedSincePublish(publishDate) {
      const second = 1000
      const minute = second * 60
      const hour = minute * 60
      const day = hour * 24
      const week = day * 7

      const now = Date.now()
      publishDate = publishDate.valueOf()
      var dateDiff = now - publishDate

      var weeks = Math.floor(dateDiff / week)
      dateDiff %= week
      var days = Math.floor(dateDiff / day)
      dateDiff %= day
      var hours = Math.floor(dateDiff / hour)
      dateDiff %= hour
      var minutes = Math.floor(dateDiff / minute)
      dateDiff %= minute
      var seconds = Math.floor(dateDiff / second)

      weeks = (weeks == 0)? "" : (weeks == 1)? weeks + " Week, " : weeks + " Weeks, "
      days = (days == 0)? "" : (days == 1)? days + " Day, " : days + " Days, "
      hours = (hours == 0)? "" : (hours == 1)? hours + " Hour, " : hours + " Hours, "
      minutes = (minutes == 0)? "" : (minutes == 1)? minutes + " Minute, " : minutes + " Minutes, "
      seconds = (seconds == 1)? seconds + " Second " : seconds + " Seconds "

      this.timeElapsedSincePublish = weeks + days + hours + minutes + seconds
      console.log(this.timeElapsedSincePublish)
    },
    async getVideoData() {
      var videoID = this.youtubeParser(this.URL)
      if (videoID) {
        var apiData = await axios.get("https://youtube.googleapis.com/youtube/v3/videos?part=snippet&id=" + videoID + "&key=AIzaSyCn0Z4eeX3JhZqcjjpiODD1Xx4AYrcTv0s")
        if (apiData.data.items.length == 1) {
          this.inputValidity = 'is-valid'
          var videoData = apiData.data.items[0].snippet
          console.log(videoData)
          if (videoData.thumbnails.maxres != null)
            this.videoThumbnail = videoData.thumbnails.maxres.url
          else
            this.videoThumbnail = videoData.thumbnails.standard.url
          this.videoTitle = videoData.title
          this.videoPublishDate = new Date(videoData.publishedAt)
          this.setVideoPublishDateString(this.videoPublishDate)
          this.setTimeElapsedSincePublish(this.videoPublishDate)
        } else {
          this.inputValidity = 'is-invalid'
          this.URL = ''
        }
      } else {
        this.inputValidity = 'is-invalid'
        this.URL = ''
      }
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
              <b>{{ videoTitle }}</b>
            </div>
          </div>
          <div class="col-lg text-center my-auto">
            <h3><b>Date and Time Published</b></h3>
            <div class="my-5">{{ videoPublishDateString }}</div>
            <h3><b>Time Elapsed Since Publishing</b></h3>
            <div class="my-5">{{ timeElapsedSincePublish }}</div> <!-- Placeholder -->
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
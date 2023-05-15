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
      URL: ''
    }
  },
  methods: {
    async getVideoData() {
      var apiData = await axios.get("https://youtube.googleapis.com/youtube/v3/videos?part=snippet&id=" + this.URL + "&key=AIzaSyCn0Z4eeX3JhZqcjjpiODD1Xx4AYrcTv0s")
      var videoData = apiData.data.items[0].snippet
      console.log(videoData)
      this.URL = ''
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
          <input v-model="URL" type="text" class="form-control" style="margin-right: 10px" placeholder="Enter a YouTube URL">
          <button type="submit" class="btn btn-dark">Enter</button>
        </form>
      </div>
    </div>
  </main>
  <Footer />
</template>

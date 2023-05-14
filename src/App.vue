<script>
import Header from './Header.vue'
import Footer from './Footer.vue'

export default {
  components: {
    Header
    Footer
  }
}
</script>

<template>
  <Header />
  <main>
    <div class="container">
      <h2>Find the exact date and time any YouTube video was uploaded</h2>
      <form>
        <input placeholder="Enter a YouTube URL">
        <button>Enter</button>
      </form> 
    </div>
  </main>
  <Footer />
</template>

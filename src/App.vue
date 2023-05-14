<script>
import Header from './Header.vue'
import Footer from './Footer.vue'

export default {
  components: {
    Header,
    Footer
  }
}
</script>

<template>
  <Header />
  <main>
    <div class="container">
      <div class=" container bg-light mt-5 p-5 border">
        <h2 class="text-center mb-4">Find a YouTube Upload Date</h2>
        <div class="d-flex flex-row mx-auto">
          <input type="text" class="form-control" style="margin-right: 10px" placeholder="Enter a YouTube URL">
          <button class="btn btn-dark">Enter</button>
        </div>
      </div>
    </div>
  </main>
  <Footer />
</template>

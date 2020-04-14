<template>
  <div id="app">
    <div class="container">
      <p>Image Splitter & QR Generator For Animal Crossing</p>
      <div class="git-ref">
        <a href="https://github.com/Haoxuan8/ImageSplitter-QR-Generator" target="_blank">Github</a>
      </div>
      <splitter @splitted="splitted"/>
      <div v-if="imgPieces">
          <QRcode v-for="imgPiece in imgPieces" :key="imgPiece.key"
           :imgPiece="imgPiece" 
           data-aos="fade-up" data-aos-offset="50"
            data-aos-delay="20"
            data-aos-duration="800" />
      </div>
    </div>
  </div>
</template>

<script>
import Splitter from './components/Splitter.vue'
import QRcode from './components/QRcode'
import AOS from 'aos'

export default {
  name: 'App',
  created() {
    AOS.init();
  },
  components: {
    Splitter,
    QRcode
  },
  data() {
    return {
      imgPieces: false
    }
  },
  methods: {
    splitted(imgPieces) {
      this.imgPieces = imgPieces;
    }
  }
}
</script>

<style lang="scss" scope>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

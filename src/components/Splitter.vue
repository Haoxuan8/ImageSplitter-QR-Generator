<template>
  <div>
    <div class="image-selector">
      <div class="in" v-if="!img">
        <div class="content">
          <div class="icon">
            <img src="../assets/imgs/icon_upload.svg" alt="">
            <input class="file-input" type="file" @change="inputImg" title="">
          </div>
          <div class="text">
            <span class="text-b">Select</span> your image file here
          </div>
        </div>
      </div> 
      <div v-else class="img-in">
        <input class="file-input-bg" type="file" @change="inputImg" title="">
        <img :src="img" class="img-show">
      </div>
    </div>

    <div class="splitter-params">
      <div>
        <span class='text text-b'>split-size</span>
      </div>
        <input class="size-input" type="number" v-model.lazy.number="splitSize">
      <div class="split-btn-ct">
        <button class="split-btn">SPLIT</button>
      </div>
    </div>

  </div>
</template>

<script>
export default{
  name: 'Splitter',
  data(){
    return{
      img:false,
      splitSize: 0
    }
  },
  methods:{
    inputImg(event) {
      var files = event.target.files || event.dataTransfer.files;
      if(!files.length)
        return;
      var reader = new FileReader();
      var vm = this;
      reader.onload = function(e) {
        vm.img = e.target.result;
      }
      reader.readAsDataURL(files[0]);
    }
  }
}
</script>


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
        <img :src="img.src" class="img-show" ref="imgShow">
        <div class="cropper-splitter" v-if="splitSize && hPieces < 20" :style="{top:boxSize.boxTop + '%', left:boxSize.boxLeft + '%', height:boxSize.boxHeight + '%', width:boxSize.boxWidth + '%'}">
          <div class="cont-square" v-for="indexV in vPieces" :key=indexV :style="{top:(indexV-1)*(1/vPieces)*100+ '%', width:'100%', height:(1/vPieces)*100 + '%'}">
            <div v-for="indexH in hPieces" :key=indexH :style="{height:'100%', width:(1/hPieces)*100 + '%'}" :class="{'square':true, 'bright':indexH<hPieces, 'bbottom':indexV<vPieces}">
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="splitter-params">
      <div>
        <span class='text text-b'>split-size</span>
      </div>
      <div class="size-input-container">
        <span>px</span>
        <input class="size-input" type="number" v-model.lazy.number="splitSize" @change="renderBox">
      </div>
        <div class="btn-group">
          <button class="btn-sub" @click="decrement">-</button>
          <button class="btn-plus" @click="increment">+</button>
        </div>
      <div class="split-btn-ct">
        <button class="split-btn" @click="split">SPLIT</button>
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
      splitSize: 0,
      vPieces: 0,
      hPieces: 0,
      boxSize: {
        boxTop: 0,
        boxLeft: 0,
        boxHeight: 0,
        boxWidth: 0,
        squareSizeH: 0,
        squareSizeW: 0
      }
    }
  },
  methods:{
    inputImg(event) {
      let files = event.target.files || event.dataTransfer.files;
      if(!files.length)
        return;
      let reader = new FileReader();
      let vm = this;
      reader.onload = function(e) {
        let i = new Image();
        i.src = e.target.result;
        i.onload = function() {
          vm.img = i;
        }
      }
      reader.readAsDataURL(files[0]);
      this.splitSize = 0;
      this.$emit('splitted',false);
    },
    updatePieces() {
      if(!this.img || !this.splitSize) 
        return;
      let h = this.img.naturalHeight;
      let w = this.img.naturalWidth;
      this.vPieces = Math.floor(h / this.splitSize);
      this.hPieces = Math.floor(w / this.splitSize);
    },
    renderBox() {
      this.updatePieces();
      let show_w = this.$refs.imgShow.width;
      let show_h = this.$refs.imgShow.height;
      let scale = this.img.naturalWidth/show_w;
      let renderSize = this.splitSize/scale;
      let box_h = this.vPieces * renderSize;
      let box_w = this.hPieces * renderSize;
      this.boxSize.boxTop = Math.round(((show_h - box_h)/2)/box_h * 100);
      this.boxSize.boxLeft = Math.round(((show_w - box_w)/2)/box_w * 100);
      this.boxSize.boxHeight = Math.round((this.vPieces * renderSize)/show_h * 100);
      this.boxSize.boxWidth = Math.round((this.hPieces * renderSize)/show_w * 100);
      this.boxSize.squareSizeH = Math.round(renderSize/show_h * 100);
      this.boxSize.squareSizeW = Math.round(renderSize/show_w * 100);
    },
    increment() {
      this.splitSize += 10;
      this.renderBox();
    },
    decrement() {
      if(this.splitSize >= 10)
        this.splitSize -=10;
        this.renderBox();
    },
    split() {
      let imgPieces = [];
      let canvas = document.createElement('canvas');
      let ctx = canvas.getContext('2d');
      let height = this.$refs.imgShow.naturalHeight;
      let width = this.$refs.imgShow.naturalWidth;
      let topOffset = Math.round(height*this.boxSize.boxTop/100);
      let leftOffset = Math.round(width*this.boxSize.boxLeft/100);
      canvas.width = this.splitSize;
      canvas.height = this.splitSize;
      for(let i=0;i<this.vPieces;i++){
        for(let j=0;j<this.hPieces;j++){
          ctx.drawImage(this.img,-j*this.splitSize-leftOffset,-i*this.splitSize-topOffset,width,height);

          imgPieces.push({
            key: Math.random(),
            url: canvas.toDataURL(),
            title: 'tile' + i + '-' + j
            });
        }
      }
      this.$emit('splitted',imgPieces);
    }
  }
}
</script>


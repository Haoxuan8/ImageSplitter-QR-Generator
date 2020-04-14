<template>
  <div class="qr-container">
    <div class="qr-img-container">
      <p>Origin Img</p>
      <img :src="imgPiece.url" class="qr-img">
    </div>
    <div class="canvas-container">
      <p>canvas Img</p>
      <canvas :width="edge" :height="edge" ref="canvas"></canvas>
    </div>
    <div class="code-container">
      <p>QR Code</p>
      <div ref="qrcode"></div>
    </div>
  </div>
</template>

<script>
import AcQrData from '../lib/ac_qr_data';
import {findRGB} from '../lib/ac_colors';
import qrcode from 'qrcode-generator';
import quantize from 'quantize';

qrcode.stringToBytes = function(data) { return data; };

export default{
  name: 'QRcode',
  props: {
    imgPiece: Object,
    edge: {
      type: Number,
      default: 32
    }
  },
  data(){
    return{
      title: this.imgPiece.title,
      creator: 'sun',
      creatorId: 99999,
      town: '岛',
      townId: 99999
    }
  },
  methods:{
    renderCanvas(){
      let canvas = this.$refs.canvas;
      this.ctx = canvas.getContext('2d');
      let vm = this;
      let i = new Image();
      i.onload = function() {
        vm.ctx.drawImage(i,0,0,vm.edge,vm.edge);
        vm.renderQRCode(); 
      }
      i.src = this.imgPiece.url;
    },
    readContext() {
      let pixels = [];
      for(let y=0;y<this.edge;y++){
        for(let x=0;x<this.edge;x++){
          let d = this.ctx.getImageData(x,y,1,1).data;
          if(d[3] < 100)
            pixels.push(-1);
          else 
            pixels.push([d[0],d[1],d[2]]);
        }
      }
      let colorMap = quantize(pixels,16);
      let palettes = colorMap.palette();

      this.palettes = palettes.map(p => findRGB(p));

      this.pixels = pixels.map(p => {
        if(p==-1)
          return 15;
        else
          return palettes.indexOf(colorMap.map(p));
      });
    },
    renderQRCode() {
      this.readContext();
      let buff = new AcQrData(620);
      ['title', 'creator', 'creatorId', 'town', 'townId'].forEach((k) => {
        buff[k] = this[k];
      });

      buff.palettes = this.palettes;
      buff.pixels = this.pixels;

      let qr = qrcode(0,'M');
      qr.addData(buff);
      qr.make();

      this.$refs.qrcode.innerHTML = qr.createImgTag();
    }
  },
  mounted() {
    this.renderCanvas();
  },
  updated() {
    this.renderCanvas();
  }
}
</script>
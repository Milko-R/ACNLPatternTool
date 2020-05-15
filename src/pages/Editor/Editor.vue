<template>
  <main class="editor--container">
    <div class="" style="text-align: center">
      <Palette
        :drawingTool="drawingTool"
        @change-current-color="onChangeCurrentColor"/>
    </div>
    <div class="editor--main">
    </div>
    <div class="editor--buttons">
    </div>
  </main>
</template>

<script>
/* libs */
import DrawingTool from '/libs/DrawingTool';
import ACNLFormat from '/libs/ACNLFormat';
import ACNHFormat from '/libs/ACNHFormat';
import origin from '/libs/origin';
import generateACNLQR from "/libs/ACNLQRGenerator";
import lzString from 'lz-string';
import { saveAs } from 'file-saver';
import JSZip from 'jszip';

import Palette from "./Palette.vue";

export default {
  name: 'Editor',
  components: {
    Palette
  },
  beforeRouteUpdate: function (to, from, next) {
    if (to.hash.length > 1) {
      if (to.hash.startsWith("#H:")){
        origin.view(to.hash.substring(3)).then((r)=>{
          this.drawingTool.load(r);
        });
        next();
        return;
      }
      let newHash = lzString.compressToEncodedURIComponent(this.drawingTool.toString());
      if (to.hash !== "#" + newHash) {
        this.drawingTool.load(lzString.decompressFromEncodedURIComponent(to.hash.substring(1)));
      }
    }
    next();
  },
  data: function() {
    return {
      drawingTool: new DrawingTool(),
      patternDetails: {
        patTitle: 'Empty',
        patAuthor: 'Unknown',
        patTown: 'Unknown',
        selectedTypes: [],
        selectedStyles: [],
        patType: 9,
      },
      storedAuthorHuman: false,
      fragment: "",
      patTypeName: "",
      pickPatterns: false,
      multiName: "Local Storage",
      allowMoveToLocal: true,
      origin,
      acnlMode: false,
      colorPicker: false,

      brown: '#7E7261',
      teal: '#57B7A8',
      orange: '#DC8D69',
      white: '#FFFFFF',
    };
  },
  methods: {
    colorPickerHandler(e, close=false) {
      if (this.drawingTool.currentColor !== 15) {
        const height = this.acnlMode ? 430 : 240;
        this.colorPicker = !this.colorPicker;
        if (close && this.colorPicker) this.colorPicker = false;
        this.$refs.colorPickerMenu.$el.style.height = `${((!this.colorPicker) ? 0 : height)}px`;
        return;
      }
      alert('This one has to stay transparent. :)');
    },
    update(data) {
      if (data.details) {
        this.patternDetails = {
          ...this.patternDetails,
          ...data.details,
        }
      }

      if (data.storedAuthorHuman) {
        this.storedAuthorHuman = data.storedAuthorHuman;
      }

      // todo: can we make drawingTool accept an object and update all of these that way?
      const patTown = this.patternDetails.patTown;
      const patAuthor = this.patternDetails.patAuthor;
      this.drawingTool.title = this.patternDetails.patTitle;
      if (this.drawingTool.creator[0] !== patAuthor) this.drawingTool.creator = patAuthor;
      if (this.drawingTool.town[0] !== patTown) this.drawingTool.town = patTown;
      if (this.drawingTool.patternType !== this.patternDetails.patType) {
        this.drawingTool.patternType = this.patternDetails.patType;
        this.patTypeName = this.drawingTool.typeInfo.name;
      }
    },
    onOpenLocal() {
      this.closeColorPicker();
      let tmp = {};
      for (const i in localStorage){
        if (i.startsWith("acnl_")){
          tmp[i] = new DrawingTool(lzString.decompressFromUTF16(localStorage.getItem(i)));
        }
      }
      this.multiName = "Local Storage";
      this.pickPatterns = tmp;
      this.allowMoveToLocal = false;
    },
    zipPicksAsACNL() {
      let zip = new JSZip();
      const titles = [];
      for (const i in this.pickPatterns){
        let dt = this.pickPatterns[i];
        if (!(dt instanceof DrawingTool)){dt = new DrawingTool(dt);}
        let ext = ".acnl";
        if (dt.pattern instanceof ACNHFormat){ext = ".acnh";}
        let title = dt.title + ext;
        let k = 1;
        while(titles.includes(title)) {
          title = dt.title + "(" + k + ")" + ext;
          k++;
        }
        zip.file(title, dt.toBytes());
        titles.push(title);
      }
      zip.generateAsync({type:"blob"}).then((d)=>{saveAs(d, "patterns.zip");});
    },
    async zipPicksAsPNG() {
      let zip = new JSZip();
      const titles = [];
      for (const i in this.pickPatterns){
        let dt = this.pickPatterns[i];
        if (!(dt instanceof DrawingTool)){dt = new DrawingTool(dt);}
        const img = await generateACNLQR(dt);
        let title = dt.title + ".png";
        let k = 1;
        while(titles.includes(title)) {
          title = dt.title + "(" + k + ")" + ".png";
          k++;
        }
        zip.file(title, img.substr(22), {base64:true});
        titles.push(title);
      }
      zip.generateAsync({type:"blob"}).then((d)=>{saveAs(d, "patterns.zip");});
    },
    async zipPicksAsBoth() {
      let zip = new JSZip();
      const titles = [];
      for (const i in this.pickPatterns){
        let dt = this.pickPatterns[i];
        if (!(dt instanceof DrawingTool)){dt = new DrawingTool(dt);}
        let ext = ".acnl";
        if (dt.pattern instanceof ACNHFormat){ext = ".acnh";}
        let ancl_title = dt.title + ext;
        let k = 1;
        while(titles.includes(ancl_title)) {
          ancl_title = dt.title + "(" + k + ")" + ext;
          k++;
        }
        const img_title = ancl_title.replace(ext, ".png");
        zip.file(ancl_title, dt.toBytes());
        const img = await generateACNLQR(dt);
        zip.file(img_title, img.substr(22), {base64:true});
        titles.push(ancl_title);
      }
      zip.generateAsync({type:"blob"}).then((d)=>{saveAs(d, "patterns.zip");});
    },
    async downTex() {
      const img = this.$refs.canvas3.toDataURL("image/png");
      saveAs(img, this.drawingTool.title+"_texture.png");
    },
    async onOpenDB() {
      this.$router.push("/browse");
    },
    onLocalSave() {
      localStorage.setItem("acnl_"+this.drawingTool.fullHash, lzString.compressToUTF16(this.drawingTool.toString()));
    },
    picksToLocal() {
      for (const i in this.pickPatterns){
        localStorage.setItem("acnl_"+this.pickPatterns[i].fullHash, lzString.compressToUTF16(this.pickPatterns[i].toString()));
      }
    },
    toolChange(newTool) {
      this.drawingTool.drawHandler = newTool;
    },
    toolChangeAlt(newTool) {
      this.drawingTool.drawHandlerAlt = newTool;
    },
    downACNL() {
      const blob = new Blob([this.drawingTool.toBytes()], {"type": "application/octet-stream"});
      let ext = ".acnl";
      if (this.drawingTool.pattern instanceof ACNHFormat){ext = ".acnh";}
      saveAs(blob, this.drawingTool.title+ext);
    },
    onColorPicked: function(color) {
      const currentColor = this.drawingTool.currentColor;
      if (this.drawingTool.getPalette(currentColor) === color) return;
      this.drawingTool.pushUndo();
      this.drawingTool.setPalette(this.drawingTool.currentColor, color);
      console.log(`color picked: ${color}`);
    },
    onChangeCurrentColor: function(idx) {
      if (this.drawingTool.currentColor === idx) return;
      this.drawingTool.currentColor = idx;
      this.drawingTool.onColorChange();
      console.log(`changed current color: ${idx}`);
    },
    onLoad: async function(t) {
      let patStr = this.drawingTool.toString();
      this.patType = this.drawingTool.patternType;
      this.patTypeName = this.drawingTool.typeInfo.name;
      this.patTitle = this.drawingTool.title;
      this.patAuthor = this.drawingTool.creator[0];
      this.patTown = this.drawingTool.town[0];

      // need to wait 2 ticks before access ref in portal
      // AFTER setting isOpenModal to true
      // https://portal-vue.linusb.org/guide/caveats.html#provide-inject
      await this.$nextTick();
      await this.$nextTick();

      /*
      const newHash = lzString.compressToEncodedURIComponent(patStr);
      const newPixHash = "#H:"+this.drawingTool.pixelHash;
      if (this.$router.currentRoute.hash !== "#" + newHash && this.$router.currentRoute.hash !== newPixHash) {
        this.$router.push({ hash: newHash });
      }
      */
      return;
    },
    extLoad: function(data) {
      this.drawingTool.load(data);
    },
    extMultiLoad: function(data) {
      this.multiName = "Load which?";
      this.pickPatterns = data;
      this.allowMoveToLocal = true;
    },
    pickPattern: function(p) {
      this.extLoad(p);
      this.pickPatterns = false;
    },
    closePicks: function() {
      this.pickPatterns = false;
    },
  },
  mounted: function() {
    // if (localStorage.getItem('author_acnl')){
    //   this.drawingTool.authorStrict = localStorage.getItem('author_acnl');
    //   this.storedAuthorHuman = `${this.drawingTool.creator[0]} / ${this.drawingTool.town[0]}`;
    // }
    // this.drawingTool.addCanvas(this.$refs.canvas1, {grid:true});
    // this.drawingTool.addCanvas(this.$refs.canvas2);
    // this.drawingTool.addCanvas(this.$refs.canvas3);
    // this.drawingTool.onLoad(this.onLoad);
    // if (this.$router.currentRoute.hash.length > 1){
    //   const hash = this.$router.currentRoute.hash.substring(1);
    //   if (hash.startsWith("H:")) {
    //     origin.view(hash.substring(2)).then((r)=>{
    //       this.drawingTool.load(r);
    //     });
    //   } else {
    //     this.drawingTool.load(lzString.decompressFromEncodedURIComponent(hash));
    //   }
    // }
    // else{
    //   this.onLoad();
    //   this.drawingTool.render();
    // }

    // // todo: can make this more vue-like
    // document.addEventListener('keydown', (e) => {
    //   if (e.ctrlKey && e.key === 'Z') {
    //     this.drawingTool.redo();
    //     e.preventDefault();
    //     return;
    //   }
    //   if (e.ctrlKey && e.key === 'z') {
    //     this.drawingTool.undo();
    //     e.preventDefault();
    //     return;
    //   }
    // });
  },
}
</script>

<style lang="scss" scoped>
  @import "../../styles/colors";

  .editor--container {

  }
</style>
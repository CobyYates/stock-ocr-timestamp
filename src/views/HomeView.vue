<template>
  <div id="app">
    <v-btn @click="recognize" color="primary" class="mb-5">Recognize</v-btn>
    <div class="container">
      <img
        id="text-img"
        alt="Vue logo"
        width="800"
        height="600"
        src="../assets/bitcoin.jpg"
      />
    </div>
    <h1>
      Stock Code:
      <span class="font-weight-light primary--text">{{ result }}</span>
    </h1>

    <iframe
      width="1000"
      height="560"
      src="https://www.youtube.com/embed/DoJz9Fle2ug"
      title="YouTube video player"
      frameborder="0"
      id="first"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
      allowfullscreen
    ></iframe>
    <canvas id="testCanvas" width="200" height="40" class="elevation-4" />
    <v-btn @click="getData"> Check Video </v-btn>
    <v-btn @click="tryimage"> Convert Video </v-btn>

    <div v-html="test" v-if="show" id="second" />

    <iframe
      width="1000"
      height="560"
      src="https://www.youtube.com/embed/jLZqzIyruRk"
      title="YouTube video player"
      frameborder="0"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
      allowfullscreen
    ></iframe>
  </div>
</template>

<script>
/* eslint-disable */
import { createWorker, PSM, OEM } from "tesseract.js";
import domtoimage from "dom-to-image";
const worker = createWorker({
  logger: (m) => console.log(m),
});
export default {
  data() {
    return {
      result: "",
      code: '<p>this is awesome</p>',
      test: null,
      show: false,
    };
  },
  computed: {
    testing() {
      return this.test;
    },
  },
  methods: {
    async recognize() {
      const img = document.getElementById("text-img");
      const c = document.createElement("canvas");
      c.width = 200;
      c.height = 40;
      c.getContext("2d").drawImage(img, 665, 0, 650, 40, 0, 0, 600, 40);
      console.log(c);
      await worker.load();
      await worker.loadLanguage("eng");
      await worker.initialize("eng", OEM.LSTM_ONLY);
      await worker.setParameters({
        tessedit_pageseg_mode: PSM.SINGLE_BLOCK,
      });
      const {
        data: { text },
      } = await worker.recognize(c);
      this.result = text;
      console.log(text);
    },
    async tryimage() {
      const image = document.getElementById("first");
      let test = await domtoimage.toSvg(image).then(function (dataUrl) {
        return dataUrl;
      });

      this.test = test;
      this.show = true;
      console.log(this.test);
    },
    async getData() {
      const image = document.getElementById("second");
      const canvas = document.getElementById("testCanvas");
      let ctx = canvas.getContext("2d");

      ctx.drawImage(image, 665, 0, 650, 40, 0, 0, 600, 40);
    },
  },
  mounted() {},
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container {
  position: relative;
}

.test {
  position: absolute;
  left: 620px;
  top: 0;
  border: 1px solid red;
  width: 200px;
  height: 40px;
}

.ytp-chrome-top {
  display: none !important;
}
</style>

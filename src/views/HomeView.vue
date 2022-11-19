<template>
  <div id="app">
    <button v-on:click="recognize">recognize</button>
    <div class="container">
      <img
        id="text-img"
        alt="Vue logo"
        width="800"
        height="600"
        src="../assets/bitcoin.jpg"
      />
      <div class="test"></div>

      <canvas id="testCanvas" width="200" height="40" class="elevation-4" />
    </div>
    <p>{{ result }}</p>
  </div>
</template>

<script>
/* eslint-disable */
import { createWorker, PSM, OEM } from "tesseract.js";
const worker = createWorker({
  logger: (m) => console.log(m),
});
export default {
  data() {
    return {
      result: "",
    };
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
  },
  mounted() {
    const image = document.getElementById("text-img");
    const canvas = document.getElementById("testCanvas");
    let ctx = canvas.getContext("2d");

    ctx.drawImage(image, 665, 0, 650, 40, 0, 0, 600, 40);
  },
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
</style>

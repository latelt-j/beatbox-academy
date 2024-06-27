<template>
  <div class="root">
    <div class="header">
      <img src="./assets/subtitle.png" class="subtitle" />
      <img src="./assets/title.png" class="title" />
    </div>
    <canvas id="myCanvas" width="300" height="300"></canvas>
    <div class="player-block">
      <button class="player" @click="readAudio('capuche')">
        <img class="img-sound" src="./assets/hood-img.png" />
        <img class="title-sound" src="./assets/hood.png" />
      </button>
      <button class="player" @click="readAudio('biscotte')">
        <img class="img-sound" src="./assets/biscotte-img.png" />
        <img class="title-sound" src="./assets/biscotte.png" />
      </button>
      <button class="player" @click="readAudio('prochainement')">
        <img class="img-sound" src="./assets/soon-img.png"  />
        <img class="title-sound" src="./assets/upcoming.png" />
      </button>
    </div>
    <canvas ref="canvas" width="800" height="400"></canvas>
  </div>
</template>

<script>
import audio1 from "./assets/pause-ta-capuche.m4a";
import audio2 from "./assets/petite-biscotte.m4a";
import audio3 from "./assets/prochainement.m4a";

export default {
  name: 'App',
  data() {
    return {
      audioContext: new (window.AudioContext || window.webkitAudioContext)(),
      currentAudio: null,
      analyser: null,
      dataArray: null,
      requestId: null,
    };
  },
  methods: {
    initAudio(selectedAudio) {
      // Check if an audio object is already playing, if so, stop and reset
      if (this.currentAudio && !this.currentAudio.paused) {
        this.currentAudio.pause();
        this.currentAudio.currentTime = 0;
      }
      this.currentAudio = new Audio(selectedAudio);
      const track = this.audioContext.createMediaElementSource(this.currentAudio);
      this.analyser = this.audioContext.createAnalyser();
      track.connect(this.analyser);
      this.analyser.connect(this.audioContext.destination);
      this.analyser.fftSize = 2048;
      this.dataArray = new Uint8Array(this.analyser.fftSize);
      this.draw();
      this.currentAudio.play();
    },
    draw() {
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');
      const width = canvas.width;
      const height = canvas.height;
      this.requestId = requestAnimationFrame(this.draw);
      ctx.clearRect(0, 0, width, height);
      this.analyser.getByteTimeDomainData(this.dataArray);
      ctx.lineWidth = 2;
      ctx.strokeStyle = 'rgb(57, 255, 20)'; // Un vert néon lumineux
      ctx.beginPath();
      const sliceWidth = width * 1.0 / this.dataArray.length;
      let x = 0;
      for (let i = 0; i < this.dataArray.length; i++) {
        const v = this.dataArray[i] / 128.0;
        const y = v * height / 2;
        if (i === 0) {
          ctx.moveTo(x, y);
        } else {
          ctx.lineTo(x, y);
        }
        x += sliceWidth;
      }
      ctx.lineTo(canvas.width, canvas.height / 2);
      ctx.stroke();
    },
    readAudio(audioKey) {
      let selectedAudio;
      switch (audioKey) {
        case 'capuche':
          selectedAudio = audio1;
          break;
        case 'biscotte':
          selectedAudio = audio2;
          break;
        case 'prochainement':
          selectedAudio = audio3;
          break;
        default:
          selectedAudio = audio1;
      }
      this.initAudio(selectedAudio);
    }
  },
  mounted() {
    // this.initAudio(); No default audio to play
  },
  beforeDestroy() {
    if (this.requestId) {
      cancelAnimationFrame(this.requestId);
    }
    if (this.currentAudio) {
      this.currentAudio.pause();
    }
    this.audioContext.close();
  }
}
</script>

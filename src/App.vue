<template>
  <div class="root">
    <button class="player" @click="readAudio('capuche')">LA CAPUCHE 🥷</button>
    <button class="player" @click="readAudio('biscotte')">PETITE BISCOTTE 🍞</button>
    <button class="player" @click="readAudio('prochainement')">PROCHAINEMENT ⌛</button>
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
      currentAudio: null
    };
  },
  methods: {
    readAudio(audioKey) {
      // Stop the current audio if it is playing
      if (this.currentAudio && !this.currentAudio.paused) {
        this.currentAudio.pause();
        this.currentAudio.currentTime = 0; // Optionally reset the audio position
      }

      // Select the appropriate audio file
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
          selectedAudio = audio1; // Default case if needed
      }

      // Create a new audio object and play it
      this.currentAudio = new Audio(selectedAudio);
      this.currentAudio.play();
    }
  }
}
</script>

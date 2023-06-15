<template>
  <div class="scanario">
    <div class="plan">
      <div
        v-for="(act, ak) in acts"
        :key="ak"
        v-on:click="setScene(ak)"
        :class="[
          'plan__item',
          { 'plan__item--active': scene == ak },
          { 'plan__item--checked': scene > ak },
        ]"
      >
        <em
          ><small>{{ ak }}</small></em
        >
        {{ act.name }}
        <br />
        <small
          ><b>&nbsp;{{ act.sound ? "Audio" : "" }}</b></small
        >
      </div>
    </div>
    <div class="scene">
      <h1 class="scene__scenario--title">
        {{ title }} <strong>{{ currentStep }}</strong>
      </h1>
      <div>
        <h2 class="scene__act-name">{{ currentAct.name }}</h2>
        <h3 class="scene__act-person">{{ currentAct.person }}</h3>
      </div>
      <div class="scene__act-warp">
        <div class="scene__act-text">
          <p>
            {{ currentAct.text }}
          </p>
          <canvas ref="vuMeter" height="64" width="570"></canvas>
          <br />
          <audio
            tabindex="0"
            class="scene__audio"
            ref="audio"
            :src="currentAct.sound"
            controls="true"
            v-if="currentAct.sound && currentAct.sound != ''"
          ></audio>
        </div>
        <div class="scene__act-comment">
          <em>{{ currentAct.comment }}</em>
        </div>
      </div>
      {{ currentAct.sound }}
    </div>
    <div class="panel">
      <span>
        Scena: <b>{{ currentStep }}</b>
      </span>
      <div>
        <button v-on:click="decreaseScene()">←</button>
        <button v-on:click="increaseScene()">→</button>
      </div>
      <b>{{ time }}</b>
    </div>
  </div>
</template>

<script>
import scenario from "../scenarios/biesiada-2023.js";

export default {
  name: "Inspicjent",
  data() {
    return {
      time: null,
      scene: 0,
    };
  },
  computed: {
    acts() {
      return scenario ? scenario.acts : [];
    },
    title() {
      return scenario.name;
    },
    currentStep() {
      return `${this.scene + 1}/${this.acts.length}`;
    },
    currentAct() {
      return this.acts[this.scene];
    },
  },
  methods: {
    getTime() {
      setInterval(() => {
        var today = new Date();
        this.time =
          today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
      }, 1000);
    },
    handleArrows() {
      document.onkeydown = (e) => {
        e = e || window.event;

        if (e.keyCode == "38") {
          // up arrow
        } else if (e.keyCode == "40") {
          // down arrow
        } else if (e.keyCode == "37") {
          // left arrow
          this.decreaseScene();
        } else if (e.keyCode == "39") {
          // right arrow
          this.increaseScene();
        }
      };
    },
    increaseScene() {
      this.scene = this.scene == this.acts.length - 1 ? this.scene : this.scene + 1;
    },
    decreaseScene() {
      this.scene = this.scene == 0 ? this.scene : this.scene - 1;
    },
    setScene(index) {
      this.scene = index;
    },
    createVUMeter() {
      const canvas = this.$refs.vuMeter;
      const ctx = canvas.getContext("2d");
      const audioContext = new (window.AudioContext || window.webkitAudioContext)();
      const audioPlayer = this.$refs.audio;
      if (audioPlayer) {
        const analyser = audioContext.createAnalyser();
        analyser.fftSize = 512;
        const source = audioContext.createMediaElementSource(audioPlayer);

        source.connect(analyser);
        analyser.connect(audioContext.destination);

        const bufferLength = analyser.frequencyBinCount;
        const dataArray = new Uint8Array(bufferLength);

        const drawMeter = () => {
          requestAnimationFrame(canvas ? drawMeter : false);

          analyser.getByteFrequencyData(dataArray);

          const barWidth = 6;
          let x = 0;

          ctx.fillStyle = "black";
          ctx.fillRect(0, 0, canvas.width, canvas.height);

          for (let i = 0; i < bufferLength; i++) {
            const barHeight = canvas.height * (dataArray[i] / 255);
            const hue = (i / bufferLength) * 1024;
            ctx.fillStyle = `hsl(${hue}, 100%, 50%)`;
            ctx.fillRect(x * 1.8, canvas.height - barHeight, barWidth, barHeight);
            x += barWidth;
          }
        };

        canvas ? drawMeter() : false;
      }
    },
  },
  watch: {
    scene: {
      handler() {
        if (this.acts[this.scene].sound) {
          setTimeout(() => {
            this.$refs.audio.focus();
            this.createVUMeter();
          }, 100);
        }
      },
      deep: true,
    },
  },
  mounted() {
    this.getTime();
    this.handleArrows();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.scanario {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  padding: 0.5rem;
  position: relative;
  height: 100%;
}
.plan {
  margin: 0.5rem;
  padding: 1rem;
  border-right: 4px solid #444;
  height: 95%;
  overflow-y: scroll;
  min-width: 250px;
  text-align: left;
}
.plan__item {
  border-bottom: 1px solid #333;
  padding: 0.5rem;
  cursor: pointer;
  &:hover {
    border-bottom: 1px solid #fff;
  }
  &--active {
    border-left: 4px solid #cdf100;
    color: #cdf100;
    font-weight: bold;
  }
  &--checked {
    border-left: 4px dashed #008d7a;
    color: #008d7a;
  }
}
.scene {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
  &__scenario--title {
    font-size: 1.5rem;
    text-align: right;
    width: 100%;
    display: block;
    margin: 0;
    position: absolute;
    top: 1rem;
    right: 1rem;
    color: #444;
    strong {
      color: #cdf100;
      border-left: 3px solid #cdf100;
      padding-left: 0.5rem;
    }
  }
  &__act-name {
    font-size: 2.4rem;
    margin: 1rem;
  }
  &__act-person {
    font-size: 1.7rem;
    margin: 1rem;
  }
  &__act-warp {
    padding: 2rem;
    font-size: 1em;
  }
  &__act-text {
    line-height: 3rem;
    padding: 2rem;
  }
  &__act-comment {
    border-left: 2px dashed #444;
    padding-left: 1rem;
    color: #888;
    display: inline-block;
    text-align: right;
    min-width: 200px;
    font-size: 1.25rem;
  }
  &__audio {
    width: 100%;
  }
}
.panel {
  width: 100%;
  padding: 0.5em;
  background-color: #444;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
  button {
    font-weight: bold;
    background-color: #fff;
    padding: 0.25rem;
    border: 0;
    margin: 0 0.25rem;
    font-size: 1rem;
    cursor: pointer;
  }
}
</style>

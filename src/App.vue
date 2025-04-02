<template id="app">
  <div class="wrapper">
    <div class="wheel-wrapper spin">
      <WheelSvg
        :hsl="hsl"
        :colors="['red', 'yellow',]"
        :colorMode="colorMode"
        :entriesArray="entriesArray"
      ></WheelSvg>
    </div>
    <!-- <div>
      <button id="plus" @click="numberOfSlices++">+</button>
      <span> {{ numberOfSlices }}</span>
      <button id="minus" @click="numberOfSlices = Math.max(numberOfSlices - 1, 0)">-</button>
      <button @click="numberOfSlices = 1">reset</button>
  </div>
  <div>
    <input type="checkbox" name="isHSL" @input="onSwitch" checked/>
    <label for="isHSL">Is HSL? </label>
  </div>
  <div class="hsl-btns" :style="`visibility: ${colorMode === 'hsl' ? 'visible' : 'hidden'}`">
    <div>
      <label for="hueStart">hue start: </label>
      <input type="range" min="0" max="360" :value="hsl.startHue"
      @input="hsl.startHue = Number($event.target.value)" />
    </div>
    <div>
        <button id="plus-huejump" @click="hsl.hueJump++">+</button>
        <label for="hueJump">hue jump: </label>
        <span> {{ hsl.hueJump }}</span>
        <button id="minus-huejump" @click="hsl.hueJump = Math.max(hsl.hueJump - 1, 0)">-</button>
        <button @click="hsl.hueJump = 0">reset</button>
    </div>
  </div> -->
    <button id="spin" @click="spin">Spin!</button>
  </div>
</template>

<script>
import WheelSvg from "./components/WheelSvg.vue"

  export default {
    components: { WheelSvg },
    data() {
      return {
        colorMode: 'hsl',
        entriesArray: ['אימון 1', "אימון 2", "אימון 3", "אימון 5"],
        hsl: {
          startHue: 20,
          hueJump: 23
        },
        rotationAngle: 2 * Math.PI / 16,
      }
    },
    methods: {
      onSwitch () {
        if (this.colorMode === "hsl") {
          this.colorMode = "array";
        } else {
          this.colorMode = "hsl";
        }
      },
      spin() {
        const randomInt = Math.floor(Math.random() * this.entriesArray.length);
        const randomFullSpins = Math.floor(Math.random() * (4 - 2) + 1);
        this.rotationAngle += 2*Math.PI * randomFullSpins;
        this.rotationAngle += randomInt * this.radianPerSlice;
      }
    },
    computed: {
      radianPerSlice() {
        return (2 * Math.PI / this.entriesArray.length)
      },
    }
  }

</script>

<style scoped>
    .wrapper {
        display: flex;
        flex-direction: column;
        gap: 2rem;
        justify-content: center;
        align-items: center;
        width: 100%;
        height: 100%;
    }

    button {
        background-color: pink;
        border: solid black 1px;
        margin-inline: 1rem;
        padding: 0.25rem 0.5rem;
        aspect-ratio: 1/1;
        height: 48px;
        border-radius: 2rem 2rem;
        cursor: pointer;
    }

    .hsl-btns {
      display: contents;
    }

    .spin {
      transform: rotate(v-bind("`${rotationAngle}rad`"));
      transition: transform 3s ease-in;
    
    }
</style>

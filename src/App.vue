<template id="app">
  <div class="wrapper">
    <WheelSvg
      :hsl="hsl"
      :colors="['red', 'yellow',]"
      :colorMode="colorMode"
      :numberOfSlices="numberOfSlices"
    ></WheelSvg>
            <div>
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
            <label for="hueJump">hue jump: </label>
            <input type="number" min="0" max="360" v-model="hsl.hueJump" />
          </div>
        </div>
        
  </div>
</template>

<script>
import WheelSvg from "./components/WheelSvg.vue"

  export default {
    components: { WheelSvg },
    data() {
      return {
        colorMode: 'hsl',
        numberOfSlices: 8,
        hsl: {
          startHue: 120,
          hueJump: 5
        }
      }
    },
    methods: {
      onSwitch () {
        if (this.colorMode === "hsl") {
          this.colorMode = "array";
        } else {
          this.colorMode = "hsl";
        }
      }
    },
    // computed: {
    //   hsl() {
    //     return {
    //       startHue: this.startHue,
    //       hueJump: this.hueJump
    //     }
    //   }
    // }
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
</style>

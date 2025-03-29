<template>
        <!-- last two values in viewbox are height and width -->
        <svg xmlns="http://www.w3.org/2000/svg" class="svg" viewBox="-50 -50 100 100" fill="none">
            <defs>
                <path id="slice"
            :d="
            `M ${cx}, ${cy}
            h ${radius}
            A ${radius} ${radius} 0 0 1 ${wantedX} ${wantedY}
            L ${cx} ${cy}
            `" stroke-width="1" stroke="black" ></path>
            </defs>
            <circle :cx="cx" :cy="cy" :r="radius" fill="grey"/>
            <use href="#slice" 
            v-for="n in numberOfSlices"
            :transform="`rotate( ${(n)*(360/numberOfSlices)}, ${cx}, ${cy} )`"
            :fill="chooseColor(n)" :key="`${colorMode}-${hslObj.startHue}-${n}`"></use>
        </svg>
</template>

<script>

export default {
        props: {"colorMode": {
            // is an array is passed and this prop is not defined - uses the array
            type: String,
            validator(value, props) {
                // The value must match one of these strings
                return ['array', 'hsl'].includes(value);
            },
            default: "array"
        },
        "hsl": {
            type: Object,
            validator(value) {
                for (let key of Object.keys(value)) {
                    if (!["startHue", "hueJump", "saturation", "lightness"].includes(key)) {
                        return false;
                    }
                    if (typeof(value[key]) !== 'undefined' && typeof(value[key]) !== 'number') {
                        return false;
                    }
                }
                return true;
            }
        },
        "colors": {type: Array,
                    default: ["red", "orange", "yellow", "green", "blue", "purple", "pink"]
        },
        "numberOfSlices": {
            required: true,
            type: Number
        }
            
    },
    data() {
        return {
            radius: 49,
            cy: 0,
            cx: 0,
        }
    },
    methods: {
        chooseColor(n) {
            console.log('choose color');
            if (this.colors && this.colorMode === 'array') {
                    return(this.colors[(n - 1) % (this.colors.length)])
            } else {
                return `hsl(${this.hslObj.hueJump * (n - 1) + this.hslObj.startHue}deg ${this.hslObj.saturation}% ${this.hslObj.lightness}%)`;
            }
        }
    },
    computed: {
        radianPerSlice() {
            return (2 * Math.PI / this.numberOfSlices)
        },
        wantedX() {
            return Math.cos(this.radianPerSlice) * this.radius + this.cx;
        },
        wantedY() {
            return Math.sin(this.radianPerSlice) * this.radius + this.cy;
        },
        hslObj() {
            return {
                startHue: this.hsl.startHue || 0,
                hueJump: this.hsl.hueJump || 22,
                saturation: this.hsl.saturation || 95,
                lightness: this.hsl.lightness || 60
            }
            }
    }
}
</script>

<style scoped>

    .svg {
        height: 13rem;
        aspect-ratio: 1/1;
    }

</style>
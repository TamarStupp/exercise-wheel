<template>
    <!-- last two values in viewbox are height and width -->
    <svg xmlns="http://www.w3.org/2000/svg" class="svg" id="wheelSvg" viewBox="-50 -50 100 100" fill="none" ref="svg">
        <defs>
            <path id="slice"
        :d="
        `M ${cx}, ${cy}
        h ${radius}
        A ${radius} ${radius} 0 ${Number(numberOfSlices < 2)} 1 ${wantedX} ${wantedY}
        L ${cx} ${cy}
        `" stroke-width="1" stroke="black" ></path>

        <path id="textPath"
            :d="`M ${cx}, ${cy}
                L ${textPathX} ${textPathY}
                M ${cx}, ${cy - lineHeight}
                L ${textPathX} ${textPathY - lineHeight}
                M ${cx}, ${cy - 2*lineHeight}
                L ${textPathX} ${textPathY - 2*lineHeight}`" stroke="pink" stroke-width="2"/>
        </defs>
        <!-- colorMode: array -->
         <g v-if="colorMode === 'array' && colors">
             <use href="#slice" 
             v-for="n in numberOfSlices"
             :transform="`rotate( ${(n)*(360/numberOfSlices)}, ${cx}, ${cy} )`"
             :fill="colors[(n - 1) % (colors.length)]"></use>
         </g>
         <g v-else>
            <g v-for="n in numberOfSlices"
            :transform="`rotate( ${(n)*(360/numberOfSlices)}, ${cx}, ${cy} )`">
                <use href="#slice" 
                :fill="`hsl(${this.hslObj.hueJump * (n - 1) + this.hslObj.startHue}deg ${this.hslObj.saturation}% ${this.hslObj.lightness}%)`"></use>
                <text :style="`font-size: clamp(0.5rem, ${radiusInPx/text.length}px, 2rem)`" fill="black"> 
                        <textPath href="#textPath">{{  text }} </textPath>
                    </text>
                <!-- <use href="#textPath"></use> -->
            </g>
         </g>
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
        text: 'hi gffds fdasaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa',
        svgWidth: 0,
        lineHeight: -6
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
    },
    radiusInPx() {
        return (this.radius * this.svgWidth)/100;
    },
    textPathX () {
        return Math.cos(this.radianPerSlice / 2) * this.radius + this.cx;
    },
    textPathY () {
        return Math.sin(this.radianPerSlice / 2) * this.radius + this.cy;
    },
    // lineHeight() {
    //     if (window.innerWidth > 1024) {
    //         return 25;
    //     } else {
    //         return 50;
    //     }
    // }
},
mounted () {
    this.svgWidth = Number(this.$refs.svg.getBoundingClientRect().width)
}
}
</script>

<style scoped>

.svg {
    height: 13rem;
    aspect-ratio: 1/1;
}

</style>
<template>
    <!-- last two values in viewbox are height and width -->
    <svg xmlns="http://www.w3.org/2000/svg" class="svg" id="wheelSvg" viewBox="-50 -50 100 100" fill="none" ref="svg">
        <defs>
            <path id="slice"
        :d="
        `M ${cx}, ${cy}
        h ${radius}
        A ${radius} ${radius} 0 ${Number(numberOfSlices < 2)} 1 ${wantedX} ${lowerLine(wantedX)}
        L ${cx} ${cy}
        `" stroke-width="1" stroke="black" ></path>

        <clipPath id="myClip">
            <!--
              Everything outside the circle will be
              clipped and therefore invisible.
            -->
            <circle cx="40" cy="35" r="35" />
          </clipPath>

        <path id="textPath"
            :d="`M ${cx}, ${cy}
                L ${textPathX} ${f(textPathX)}
                M ${textPathXMoved1}, ${g(textPathXMoved1, lineHeight)}
                L ${textPathX + lineHeight} ${g(textPathX + lineHeight ,lineHeight)}
                M ${textPathXMoved2}, ${g(textPathXMoved2, lineHeight * 2)}
                L ${textPathX + lineHeight} ${g(textPathX + lineHeight , 2*lineHeight)}`" stroke="pink" stroke-width="2"/>
        </defs>
        <!-- colorMode: array -->
         <g v-if="colorMode === 'array' && colors">
             <use href="#slice" 
             v-for="n in numberOfSlices"
             :transform="`rotate( ${(n)*(360/numberOfSlices)}, ${cx}, ${cy} )`"
             :fill="colors[(n - 1) % (colors.length)]"></use>
         </g>
         <g v-else>
            <!-- v-for="n in numberOfSlices" -->
            <g 
            v-for="(value, index) in entriesArray"
            :transform="`rotate( ${(index)*(360/numberOfSlices)}, ${cx}, ${cy} )`">
                <use href="#slice" 
                :fill="`hsl(${this.hslObj.hueJump * (index - 1) + this.hslObj.startHue}deg ${this.hslObj.saturation}% ${this.hslObj.lightness}%)`"></use>
                <text :style="`font-size: clamp(0.5rem, ${radiusInPx/(value.length * 2)}px, 2rem)`" class="txt" fill="black"> 
                        <textPath startOffset="10"  href="#textPath">{{  value }} </textPath>
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
    "entriesArray": {
        required: true,
        type: Array
    }
        
},
data() {
    return {
        radius: 49,
        cy: 0,
        cx: 0,
        svgWidth: 0,
        lineHeight: -6
    }
},
methods: {
    f(x) {
        return Math.tan(this.radianPerSlice / 2) * x + this.cy - this.cx * Math.tan(this.radianPerSlice / 2)
    },
    g(x, moveDown) {
        return this.f(x) - moveDown;
    },
    lowerLine(x) {
        return Math.tan(this.radianPerSlice) * x + this.cy - this.cx * Math.tan(this.radianPerSlice)
    },
},
computed: {
    numberOfSlices() {
        return this.entriesArray.length;
    },
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
    textPathXMoved1() {
        return this.cx - this.lineHeight/(Math.tan(this.radianPerSlice) - Math.tan(this.radianPerSlice / 2))
    },
    textPathXMoved2() {
        return this.cx - this.lineHeight * 2/(Math.tan(this.radianPerSlice) - Math.tan(this.radianPerSlice / 2))
    }
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

.txt {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

</style>
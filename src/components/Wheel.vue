<template>
    <div class="wrapper">
        <div class="wrapper">
            <WheelSvg :transitionTime="transitionTime" :hsl="hsl" :colors="['red', 'yellow',]" :colorMode="colorMode"
                :entriesArray="entriesArray" :rotationAngle="rotationAngle"></WheelSvg>
            <button id="spin-btn" class="spin-btn" @click="spin" dir="rtl">סובב!</button>
        </div>
        <button class="edit-wheel" @click="onClickEdit">
            {{ isEditingMode ? 'סיים' : 'ערוך גלגל' }}
        </button>
        <div class="edit-screen" v-if="isEditingMode">
            <div class="input-flex">
                <div v-for="(exer, index) in entriesArray" :key="'exer' + index" class="input-and-btn">
                    <button class="minus" @click="entriesArray.splice(index, 1);">
                        <div class="minus-icon">-</div>
                    </button>
                    <input v-model="entriesArray[index]" type="text" class="exer-input" />
                </div>
                <button @click="entriesArray.push('')" class="plus">+</button>
            </div>
        </div>
        <div class="popup" v-show="isShowPopup">
            <div class="black">
                <div class="popup-window">
                    <div class="close-btn exit" @click="isShowPopup = false"></div>
                    התרגיל שנבחר: 
                    <br>
                    {{ entriesArray[chosenIndex] }}
                </div>
            </div>
        </div>
    </div>
</template>


<script>
import WheelSvg from "./WheelSvg.vue"

export default {
    components: { WheelSvg },
    props: ["entriesArray"],
    data() {
        return {
            colorMode: 'hsl',
            chosenIndex: -1,
            isShowPopup: false,
            hsl: {
                startHue: 20,
                hueJump: 23
            },
            rotationAngle: 0,
            isEditingMode: false,
            timeoutId: '',
            transitionTime: 3
        }
    },
    methods: {
        onSwitch() {
            if (this.colorMode === "hsl") {
                this.colorMode = "array";
            } else {
                this.colorMode = "hsl";
            }
        },
        onClickEdit() {
            if (this.isEditingMode === true) {
                this.$emit('save-to-local')
            }
            this.isEditingMode = !this.isEditingMode;
        },
        spin() {
            const randomInt = Math.floor(Math.random() * this.entriesArray.length);
            const randomFullSpins = Math.floor(Math.random() * (4 - 2) + 1);
            this.rotationAngle += 2 * Math.PI * randomFullSpins; this.rotationAngle += randomInt * this.radianPerSlice;
            if ((this.rotationAngle - Math.PI / 2 - this.radianPerSlice / 2) % (this.radianPerSlice) !== 0) {
                this.rotationAngle += ((this.radianPerSlice) - (this.rotationAngle - Math.PI / 2 - this.radianPerSlice / 2) % (this.radianPerSlice));
            }
            clearTimeout(this.timeoutId);
            this.timeoutId = setTimeout(() => {
                const radians = this.rotationAngle % (2 * Math.PI);
                let index = Math.floor(radians * this.entriesArray.length / (2 * Math.PI));
                index = (this.entriesArray.length - index) % this.entriesArray.length;
                console.log('index:', index);
                this.chosenIndex = index;
                this.isShowPopup = true;
            }, this.transitionTime * 1000);
        }
    },
    computed: {
        radianPerSlice() {
            return (2 * Math.PI / this.entriesArray.length)
        },
    },
}

</script>

<style scoped>
button {
    cursor: pointer;
}

.wrapper {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    /* height: 100%; */
    padding-top: 1rem;
}

.spin-btn {
    background-color: pink;
    border: solid black 1px;
    margin-inline: 1rem;
    padding: 0.25rem 0.5rem;
    aspect-ratio: 1/1;
    height: 48px;
    border-radius: 2rem 2rem;
    cursor: pointer;
    margin-top: 1rem;
}

.hsl-btns {
    display: contents;
}

.edit-wheel {
    background-color: pink;
    border: solid black 1px;
    margin-inline: 1rem;
    padding: 0.25rem 0.5rem;
    min-height: 48px;
    border-radius: 0.2rem 0.2rem;
    cursor: pointer;
    margin-top: 1rem;
}

.input-flex {
    max-height: 35vh;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 12px;
}

.exer-input {
    min-height: 1.5rem;
}

.minus {
    /* margin-inline: max(1rem, 36px); */
    height: 48px;
    border: none;
    background-color: transparent;
}

.minus-icon {
    box-sizing: border-box;
    background-color: pink;
    border: solid black 1px;
    padding: 0.25rem 0.6rem;
    border-radius: 2rem 2rem;
    aspect-ratio: 1 / 1;
    cursor: pointer;
}

.plus {
    box-sizing: border-box;
    background-color: pink;
    border: solid black 1px;
    padding: 0.25rem 0.6rem;
    border-radius: 2rem 2rem;
    aspect-ratio: 1 / 1;
    cursor: pointer;
    align-self: center;
}

.black {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.563);
    display: flex;
    justify-content: center;
    align-items: center;
}

.popup-window {
    background-color: azure;
    border-radius: 2rem;
    min-width: 50%;
    min-height: 50%;
    padding: 2rem;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    font-size: 2rem;
    text-align: center;
}

.exit {
    position: absolute;
    top: 1.2rem;
    left: 0.9rem;
    width: 1.6rem;
    height: 1.7rem;
    min-height: 48px;
    min-width: 48px;
    z-index: 2;
    cursor: pointer;
}

.exit::before,
.exit::after {
    content: '';
    width: 16%;
    height: 100%;
    position: absolute;
    border-radius: 6px;
    background: black;
}

.exit::before {
    transform: translate(-50%) rotate(45deg);
}

.exit::after {
    transform: translate(-50%) rotate(-45deg);
}
</style>

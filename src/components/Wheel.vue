<template>
	<div class="wrapper">
		<div class="wrapper">
			<WheelSvg :hsl="hsl" :colors="['red', 'yellow',]" :colorMode="colorMode" :entriesArray="entriesArray"
				:rotationAngle="rotationAngle"></WheelSvg>
			<button id="spin-btn" class="spin-btn" @click="spin" dir="rtl">סובב!</button>
		</div>
		<button class="edit-wheel" @click="onClickEdit">
			{{ isEditingMode ? 'סיים': 'ערוך גלגל' }}
		</button>
		<div class="edit-screen" v-if="isEditingMode">
			<div class="input-flex">
				<div v-for="(exer, index) in entriesArray" :key="'exer' + index" class="input-and-btn">
					<button class="minus" @click="entriesArray.splice(index, 1);">
						<div class="minus-icon">-</div>
					</button>
					<input v-model="entriesArray[index]" type="text" class="exer-input"/>
				</div>
				<button @click="entriesArray.push('')" class="plus">+</button>
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
			// entriesArray: ['אימון 1', "שלום לכולם היום נראה משהו מיוחד", "אימון 3", "אימון 5", "אימון 6"],
			hsl: {
				startHue: 20,
				hueJump: 23
			},
			rotationAngle: 0,
			isEditingMode: false
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
			this.rotationAngle += 2 * Math.PI * randomFullSpins;
			const radians = this.rotationAngle % (2*Math.PI);
			this.rotationAngle += randomInt * this.radianPerSlice;
			if ((this.rotationAngle - Math.PI/2 - this.radianPerSlice/2) % (this.radianPerSlice) !== 0) {
				console.log('hi');
				this.rotationAngle += ((this.radianPerSlice) - (this.rotationAngle - Math.PI/2 - this.radianPerSlice/2) % (this.radianPerSlice));
			}
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
</style>

<template id="app">
	<div class="grid">
		<div class="sidebar">
			<button v-for="(entry, index) in appData" :class="['category', currCategoryIndex === index ? 'chosenCategory': '']"
			@click="currCategoryIndex = index"
			>
			<div class="black"></div>
			{{ entry.name }}</button>
		</div>
		<Wheel :entriesArray="appData[currCategoryIndex].items" @save-to-local="saveToLocal"></Wheel>
	</div>
</template>


<script>
import Wheel from "./components/Wheel.vue"

export default {
	components: { Wheel },
	data() {
		return {
			currCategoryIndex: 0,
			appData: [
				{
					name: "כתפיים",
					items: [""]
				},
				{
					name: "חזה",
					items: [""]
				},
				{
					name: "יד קדמית",
					items: ["" ]
				},
				{
					name: "יד אחורית",
					items: [""]
				},
				{
					name: "אצבעות",
					items: [""]
				},
				{
					name: "בטן",
					items: [""]
				},
				{
					name: "גב",
					items: [""]
				},
				{
					name: "המסטרינג",
					items: [""]
				},
				{
					name: "ארבע ראשי",
					items: [""]
				},
				{
					name: "תאומים וקרסוליים",
					items: [""]
				},
			]
		}
	},
	created() {
		if (localStorage.getItem('appData')) {
			this.appData = JSON.parse(localStorage.getItem('appData'));
		}
	},
	methods: {
		saveToLocal() {
			localStorage.setItem('appData', JSON.stringify(this.appData));
		}

	}
}

</script>

<style scoped>
	.grid {
		display: grid;
		grid-template-columns: 25fr 75fr;
		width: 100%;
		height: 100%;
	}

	.category {
		cursor: pointer;
		background-color: transparent;
		outline: none;
		border: none;
		border-bottom: 1px solid black;
		position: relative;
		transition: all 0.5s;
		font-size: 1.2rem;
	}

	.category .black {
		display: block;
		position: absolute;
		bottom: -10%;
		left: 0%;
		z-index: -1;
		background-color: rgb(54, 54, 54);
		width: 100%;
		/* transition: height 3s; */
	}

	.sidebar {
		height: 100%;
		display: inline-flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-between;
		padding-block: 3rem;
		padding-right: 0.5rem;
		box-sizing: border-box;
		gap: 48px;
		overflow-y: auto;
		overflow-x: hidden;
	}

	.chosenCategory {
		color: rgb(243, 243, 243)
	}

	.chosenCategory .black {
		animation: expand 0.3s forwards;
	}

	@keyframes expand {
		from {
			height: 0%;
		} to {
			height: 120%;
		}
	}

</style>

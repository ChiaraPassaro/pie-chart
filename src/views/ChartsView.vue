<script setup lang="ts">
	import PieChart from "@/components/PieChart.vue"
	import { onMounted, ref } from "vue"

	const loading = ref(false)

	//mockup data
	const data = [
		{
			label: "Lorem",
			quantity: 70,
			color: "hsl(20 60% 50%)",
		},
		{
			label: "Ipsum Dolor",
			quantity: 10,
			color: "hsl(180 60% 50%)",
		},
		{
			label: "Sit",
			quantity: 20,
			color: "hsl(200 60% 50%)",
		},
	]
</script>
<template>
	<main>
		<header><h2>Pie Chart</h2></header>
		<PieChart v-if="data?.length" :data="data" />
	</main>
</template>

<style scoped>
	h2 {
		color: white;
		text-align: center;
		text-transform: uppercase;
		font-weight: bold;
		font-size: 2rem;
	}
	main {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		block-size: 100%;
		padding: 2rem;
	}
</style>

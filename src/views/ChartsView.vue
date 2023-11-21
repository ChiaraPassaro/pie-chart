<script setup lang="ts">
	import PieChart from "@/components/PieChart.vue"

	//mockup data
	const data = [
		{
			label: "Label 1",
			quantity: 20,
			color: "hsl(20 60% 50%)",
		},
		{
			label: "Label 2",
			quantity: 20,
			color: "hsl(180 60% 50%)",
		},
		{
			label: "Label 3",
			quantity: 20,
			color: "hsl(200 60% 50%)",
		},
		{
			label: "Label 4",
			quantity: 20,
			color: "hsl(220 60% 50%)",
		},
		{
			label: "Label 5",
			quantity: 20,
			color: "hsl(240 60% 50%)",
		},
	]
</script>
<template>
	<main>
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
		justify-content: center;
		block-size: 100%;
		padding: 2rem;
	}
</style>

<script setup lang="ts">
	import { computed, type PropType } from "vue"
	import type { PieData } from "@/types/chart"

	//we start from the props
	//we need some data to represent
	const props = defineProps({
		data: {
			type: Object as PropType<PieData[]>,
			required: true,
		},
		animationDuration: {
			type: Number,
			default: 1,
		},
	})

	//these constants are used to draw the pie
	//the radius of the circle
	const radius = 16
	//the size of the viewbox (a square)
	const viewbox = radius * 2
	//the circumference of the circle, we need it to calculate the slices sizes
	const circumference = 2 * Math.PI * radius
	//furthermore we create a mask for every slice to create a stacked pie chart
	const minRadiusMask = radius - props.data.length
	//we will see this in a little while
	const strokeWidth = 23

	//It is possible that the sum of quantities is bigger than 100%
	//We use a computed to calculate the total quantity
	const totalPercentage = computed(() =>
		props.data.reduce((totalQuantity, { quantity }) => {
			return totalQuantity + quantity
		}, 0)
	)
	//we calculate the single percentage in relation to the circumference
	const getSinglePercentage = (quantity: number) => (quantity * 100) / totalPercentage.value

	//we calculate the single percentage in relation to the circumference
	const getSlice = (quantity: number) => {
		return (circumference * getSinglePercentage(quantity)) / 100
	}

	//Every element is rotated starting from the position of the previous, we have a circle, so we need to calculate the percentage in relation to 360deg.
	const getRotate = (index: number) => {
		let angle = 0
		let counter = 0
		while (counter < index) {
			angle += (getSinglePercentage(props.data[counter].quantity) / 100) * 360
			counter++
		}
		return angle
	}

	//We use the duration inside the CSS with v-bind, but we need to add the unit
	const duration = computed(() => `${props.animationDuration}s`)

	//Each circle has its calculated delay
	const getDelay = (index: number) => `${0.5 + index / 3}s`

	//Mask and circle need an id to be bound to each other.
	const getId = (label: string, index: number) => `${label.replace(" ", "-")}-${index}`

	const colorBrightness = (color: string) => {
		const colorSplit = color.split("%")
		const lightness = parseInt(colorSplit[1]) > 30 ? parseInt(colorSplit[1]) - 30 : parseInt(colorSplit[1]) + 20

		return `${colorSplit[0]}% ${lightness}% ${colorSplit[2]}`
	}
</script>
<template>
	<div class="data__pie">
		<!--we set the viewbox-->
		<svg :viewBox="`0 0 ${viewbox} ${viewbox}`">
			<!--ve use a template for the v-for-->
			<template v-for="({ label, quantity, color }, idx) in data" :key="`${label}-${idx}`">
				<radialGradient :id="`gradient-${getId(label, idx)}`">
					<stop offset="0%" :stop-color="colorBrightness(color)" />
					<stop offset="100%" :stop-color="color" />
				</radialGradient>
				<!-- for each data we have a mask -->
				<!-- we need an id to link the mask -->
				<mask :id="getId(label, idx)">
					<!-- each mask has a different radius. We start from the lowest -->
					<!-- and we set the center of the circle at the center of the viewbox -->
					<circle :r="minRadiusMask + idx" :cx="radius" :cy="radius" fill="white" />
				</mask>

				<!-- for each data we have a group -->
				<g>
					<!--
						Vue 
							We use our constants to populate the circle attributes
							each circle has a related mask
						CSS 
							each circle has its own: 
							custom props to change the animation and color
							stroke-dasharray
							stroke-width
		
					-->
					<circle
						:r="radius"
						:cx="radius"
						:cy="radius"
						:mask="`url(#${getId(label, idx)})`"
						class="data__pie-slice"
						:style="`
							--rotate: ${getRotate(idx)}deg; 
							--color: url(#gradient-${getId(label, idx)}); 
							--delay: ${getDelay(idx)};
							stroke-dasharray:${getSlice(quantity)}px, ${circumference}px; 
							stroke-width: ${strokeWidth};
						`"
					>
						<!-- The title is for accessibility and the tooltip -->
						<title>{{ label }}: {{ quantity }}%</title>
					</circle>
				</g>
			</template>
		</svg>
	</div>
</template>

<style scoped>
	.data__pie {
		display: flex;
		justify-content: center;
		align-items: center;
		inline-size: 100%;
		block-size: 30rem;
		overflow: hidden;
	}
	.data__pie svg {
		inline-size: 30rem;
		fill: none;
	}

	/*
		Using custom props enables us to create a different delay and rotation for each circle but using the same animation.
	*/
	.data__pie circle {
		cursor: pointer;
		/*We need to set the origin to the center for the rotation*/
		transform-origin: center center;
		animation: rotate;
		animation-iteration-count: 1;
		/* the circle retains the properties set at the end of animation */
		animation-fill-mode: forwards;
		/* The v-bind method is useful to populate the CSS with props and computed that do not need other parameters. */
		animation-duration: v-bind(duration);
		/*Each circle has its own delay and color*/
		animation-delay: var(--delay);
		stroke: var(--color);
		opacity: 0;
	}
	.data__pie circle:hover {
		filter: brightness(0.8);
	}

	@keyframes rotate {
		0% {
			opacity: 0;
			transform: rotate(-180deg);
		}
		100% {
			opacity: 1;
			/*Each circle has its own rotation*/
			transform: rotate(var(--rotate));
		}
	}
</style>

<script>
	import { onMount } from 'svelte';
	import { scaleLinear } from 'd3-scale';

	export let points;

	let svg;
	let width = 500;
	let height = 250;

	const padding = { top: 20, right: 20, bottom: 20, left: 25 };

	$: xScale = scaleLinear()
		.domain([0, 20])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([0, 12])
		.range([height - padding.bottom, padding.top]);

	$: xTicks = width > 180 ?
		[0, 4, 8, 12, 16, 20] :
		[0, 10, 20];

	$: yTicks = height > 180 ?
		[0, 2, 4, 6, 8, 10, 12] :
		[0, 4, 8, 12];

	onMount(resize);

	function resize() {
		({ width, height } = svg.getBoundingClientRect());
	}
</script>

<svelte:window on:resize='{resize}'/>

<svg bind:this={svg}>
	<g class='axis y-axis'>
		{#each yTicks as tick}
			<g class='tick tick-{tick}' transform='translate(0, {yScale(tick)})'>
				<line x1='{padding.left}' x2='{xScale(22)}'/>
				<text x='{padding.left - 8}' y='+4'>{tick}</text>
			</g>
		{/each}
	</g>

	
	<g class='axis x-axis'>
		{#each xTicks as tick}
			<g class='tick' transform='translate({xScale(tick)},0)'>
				<line y1='{yScale(0)}' y2='{yScale(13)}'/>
				<text y='{height - padding.bottom + 16}'>{tick}</text>
			</g>
		{/each}
	</g>

	
	{#each points as point}
		<circle cx='{xScale(point.x)}' cy='{yScale(point.y)}' r='5'/>
	{/each}
</svg>

<style>
	svg {
		width: 90%;
		height: 90%;
		float: left;
	}

	circle {
		fill: orange;
		fill-opacity: 0.6;
		stroke: rgba(0,0,0,0.5);
	}

	.tick line {
		stroke: rgb(1, 4, 14);
		stroke-dasharray: 2;
	}

	text {
		font-size: 14px;
		fill: rgb(153, 153, 153);
	}

	.x-axis text {
		text-anchor: middle;
	}

	.y-axis text {
		text-anchor: end;
	}
</style>
<script>
	import { onMount } from 'svelte';
	import { scaleLinear } from 'd3-scale';
    import { scale } from 'svelte/types/runtime/transition';

	export let points;
	export let scale_ob;

	let svg;
	let width = 500;
	let height = 250;

	const padding = { top: 20, right: 20, bottom: 20, left: 25 };
	console.log(scale_ob)
	$: xScale = scaleLinear()
		.domain([scale_ob['min_x'], scale_ob['max_x']])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([scale_ob['min_y'], scale_ob['max_y']])
		.range([height - padding.bottom, padding.top]);
	
	let xTicks = []

	let yTicks = []
	for (let i=scale_ob['min_x'];i<=scale_ob['max_x'];i+=parseFloat(scale_ob['nodx'])){
		
		xTicks.push(i);
	}
	for (let i=scale_ob['min_y'];i<=scale_ob['max_y'];i+=parseFloat(scale_ob['nody'])){
		
		yTicks.push(i);
	}

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
		width: 100%;
		height: 100%;
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
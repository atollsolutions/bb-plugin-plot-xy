<script>
	import { onMount, afterUpdate, onDestroy,createEventDispatcher ,setContext } from 'svelte';
	import { scaleLinear } from 'd3-scale';
	
	export let points;
	export let scale_ob;
	export let points1;
	export let dr;

	let svg;
	let width = '80%';
	let height = '60%';

	const padding = { top: 20, right: 20, bottom: 5, left: 25 };
	
	let xScale, yScale, xTicks, yTicks;
	
	function calculateScales() {
		xScale = scaleLinear()
			.domain([scale_ob['min_x'], scale_ob['max_x']])
			.range([padding.left, width - padding.right]);

		yScale = scaleLinear()
			.domain([scale_ob['min_y'], scale_ob['max_y']])
			.range([height - padding.bottom, padding.top]);

		xTicks = [];
		yTicks = [];

		for (let i=scale_ob['min_x']; i<=scale_ob['max_x']; i += parseFloat(scale_ob['nodx'])) {
			xTicks.push(i);
		}

		for (let i=scale_ob['min_y']; i<=scale_ob['max_y']; i += parseFloat(scale_ob['nody'])) {
			yTicks.push(i);
		}
		console.log(xTicks)
	}
	
	onMount(() => {
		calculateScales();
		resize();
	});

	afterUpdate(() => {
		calculateScales();
	});

	onDestroy(() => {
		xScale = null;
		yScale = null;
		xTicks = null;
		yTicks = null;
	});

	function resize() {
		({ width, height } = svg.getBoundingClientRect());
	}
	$: {
        calculateScales();
    }

    // Create an event dispatcher to signal changes to other components
    const dispatch = createEventDispatcher();

    // Set the xScale and yScale in the context for child components to use
    setContext('scales', { xScale, yScale, dispatch });

</script>

<svelte:window on:resize="{resize}" />

<svg bind:this={svg}>
	{#if xScale && yScale && xTicks && yTicks}
	<g class='axis y-axis'>
		{#each yTicks as tick}
			<g class='tick tick-{tick}' transform='translate(0, {yScale(tick)})'>
				<line x1='{padding.left}' x2='{xScale(scale_ob.max_x)}'/>
				<text x='{padding.left - 8}' y='+4'>{tick}</text>
			</g>
		{/each}
	</g>

	<g class='axis x-axis'>
		{#each xTicks as tick}
		  <g class='tick' transform='translate({xScale(tick)},0)'>
			<line y1='{yScale(0)}' y2='{yScale(scale_ob.max_y)}'/>
			<text x='{xScale(tick)}' y='{height - padding.bottom + 20}' fill='black'>{tick}</text>
		  </g>
		{/each}
	  </g>

	{#each points as point}
	{#if !(isNaN(point.x) || isNaN(point.y))}
	  <circle cx='{xScale(point.x)}' cy='{yScale(point.y)}' r='5'/>
	  
	  {#if dr[point['id']]}
		{#each Object.values(dr[point['id']]) as pointt}
		  <text x="{xScale(point.x) + 20}" y="{yScale(point.y) + pointt*5}" font-size="17">{pointt}</text>
		{/each}
	  {/if}
	{/if}
  {/each}

	{/if}
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

	.v1 {
		fill: rgb(20, 40, 129);
		fill-opacity: 0.6;
		stroke: rgba(0,0,0,0.5);
	}

	text {
		font-size: 14px;
		fill: rgb(153, 153, 153);
	}

	.x-axis line,
	.y-axis line {
		stroke: #ddd;
		stroke-width: 1;
		shape-rendering: crispEdges;
	}

	.x-axis text {
		text-anchor: middle;
	}

	.y-axis text {
		text-anchor: end;
	}
</style>
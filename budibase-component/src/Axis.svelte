<script>
	import { onMount, afterUpdate, onDestroy } from 'svelte';
	import { scaleLinear, scaleSequential } from 'd3-scale';
	import { interpolateViridis } from 'd3-scale-chromatic';
  
	export let points;
	export let scale_ob;
  
	let svg;
	let size = 0.4;
	let width, height, svgSize;
  
	const padding = { top: 10, right: 10, bottom: 50, left: 50 };
  
	let xScale, yScale, xTicks, yTicks;
  
	function calculateScales() {
	  let range = Math.max(scale_ob['max_x'] - scale_ob['min_x'], scale_ob['max_y'] - scale_ob['min_y']);
	  svgSize = window.innerWidth * size;
	  width = svgSize - padding.left - padding.right;
	  height = svgSize - padding.top - padding.bottom;
	  xScale = scaleLinear()
		.domain([scale_ob['min_x'], scale_ob['max_x']])
		.range([padding.left, width - padding.right]);
  
	  yScale = scaleLinear()
		.domain([scale_ob['min_y'], scale_ob['max_y']])
		.range([height - padding.bottom, padding.top]);
  
	  xTicks = [];
	  yTicks = [];
  
	  for (let i = scale_ob['min_x']; i <= scale_ob['max_x']; i += parseFloat(scale_ob['nodx'])) {
		xTicks.push(i);
	  }
  
	  for (let i = scale_ob['min_y']; i <= scale_ob['max_y']; i += parseFloat(scale_ob['nody'])) {
		yTicks.push(i);
	  }
	}
  
	// Define a color scale based on time
	const colorScale = scaleSequential()
	  .domain([scale_ob['min_t']*100, scale_ob['max_t']*100])
	  .interpolator(interpolateViridis);
  
	afterUpdate(() => {
	  calculateScales();
	});
  
	onMount(() => {
	  calculateScales();
	});
  
	onDestroy(() => {
	  xScale = null;
	  yScale = null;
	  xTicks = null;
	  yTicks = null;
	});
  </script>
  
  <svelte:window />
  
  <svg bind:this={svg} style="width: {window.innerWidth * size}px; height: {window.innerWidth * size}px">
	{#if xScale && yScale && xTicks && yTicks}
	<g class='axis y-axis' >
		{#each yTicks as tick}
			<g class='tick tick-{tick}' transform='translate(0, {yScale(tick)})'>
				<line x1='{padding.left}' x2='{xScale(scale_ob.max_x)}'/>
				<text x='{padding.left - 8}' y='+4'>{tick}</text>
			</g>
		{/each}
	</g>

	<g class='axis x-axis' >
		{#each xTicks as tick}
			<g class='tick' transform='translate({xScale(tick)},0)'>
				<line y1='{yScale(0)}' y2='{yScale(scale_ob.max_y)}'/>
				<text y='{height - padding.bottom + 16}'>{tick}</text>
			</g>
		{/each}
	</g>
  {#each points as point}
  <circle
    cx='{xScale(point.x)}'
    cy='{yScale(point.y)}'
    r='2.2'
    fill='{colorScale(new Date(point.t).getTime()*100)}'
  />
{/each}
{/if}
</svg>
<style>
	svg {
  margin: auto;
  display: block;
		float: center;
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
  
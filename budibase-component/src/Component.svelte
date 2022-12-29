<script>
  import { getContext } from "svelte"

  const { styleable } = getContext("sdk")
  const component = getContext("component")
	import Scatterplot from "./Axis.svelte";
	import data from './paths.js';
</script>

<div class="chart">
	<h1>Canvas</h1>

	<Scatterplot points={data.a}/>
	
</div>

<style>
	.chart {
		width: 100%;
		height: 100%;
		min-height: 500px;
		max-height: 500px;
		margin: 16 16;
	}
</style>
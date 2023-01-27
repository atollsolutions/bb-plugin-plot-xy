<script>
  import { getContext } from "svelte"
  export let dataProvider;
  export let nodx;
  export let nody;
  export let xaxis;
  export let yaxis;
  
  let max_x,max_y,min_x,min_y;
  let scale_object;
  const { styleable } = getContext("sdk")
  const component = getContext("component")
	import Scatterplot from "./Axis.svelte";
	import data from './paths.js';
	let Data=[]
	console.log(dataProvider)
	$: Data =dataProvider.rows.map(xx=>{
		return {
			x:Number(xx[xaxis]),
			y:Number(xx[yaxis]),
		}
	})
	if(Data.length>0){
		data.a=Data

	}
	
	
	max_x=data.a[0]['x']
	min_x=data.a[0]['x']
	max_y=data.a[0]['y']
	min_y=data.a[0]['y']
	for(let i=1;i<data.a.length;i++){
		max_x=Math.max(max_x,data.a[i]['x'])
		min_x=Math.min(min_x,data.a[i]['x'])
		max_y=Math.max(max_y,data.a[i]['y'])
		min_y=Math.min(min_y,data.a[i]['y'])
	}
	scale_object={
		max_x:max_x,
		max_y:max_y,
		min_x:min_x,
		min_y:min_y,
		nodx:nodx,
		nody:nody,
	}
</script>

<div class="chart">
	<h1>Canvas</h1>

	<Scatterplot bind:scale_ob={scale_object} bind:points={data.a}/>
	
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
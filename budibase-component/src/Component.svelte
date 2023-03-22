<script>
	import { getContext } from "svelte"

	export let dataProvider;

	export let nodx;
	export let nody;
	export let xaxis;
	export let yaxis;
	export let tc;
	console.log(dataProvider)
	
	let max_x = 10;
  let max_y = 10;
  let min_x = 0;
  let min_y = 0;
	let scale_object;
  
	const { styleable } = getContext("sdk")
	const component = getContext("component")
	import Axis from "./Axis.svelte";
	
	
	  
	let dataa = { a: [] };
	  console.log(dataProvider)
	$: if (dataProvider) {
	  const newData = dataProvider.rows.filter(function(xx) {
  if (!xx[xaxis] || !xx[yaxis]) {
    return false; // skip
  }
  return true;
}).map(xx => {
		return {
		  x: Number(xx[xaxis]),
		  y: Number(xx[yaxis]),
		}
	  });
  
	  if (newData.length > 0) {
		dataa.a = newData;
	  } 
  
	  if( dataa.a.length>0 ){
	  max_x = Math.max(max_x, dataa.a[0]['x']);
	  min_x = Math.min(min_x, dataa.a[0]['x']);
	  max_y = Math.max(max_y,dataa.a[0]['y']);
	  min_y = Math.min(min_y, dataa.a[0]['y']);
	  
	  for (let i = 1; i < dataa.a.length; i++) {
		max_x = Math.max(max_x, dataa.a[i]['x']);
		min_x = Math.min(min_x, dataa.a[i]['x']);
		max_y = Math.max(max_y, dataa.a[i]['y']);
		min_y = Math.min(min_y, dataa.a[i]['y']);
	  }
	}
	  
	  scale_object = {
		max_x: max_x,
		max_y: max_y,
		min_x: min_x,
		min_y: min_y,
		nodx: nodx,
		nody: nody,
		xaxis: xaxis,
		yaxis: yaxis
	  };
	} 
  </script>
  
  <div class="chart">
	<h1>{tc}</h1>
	<Axis points={dataa.a} scale_ob={scale_object} />
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
<script>
	import { getContext } from "svelte"

	export let dataProvider;

	export let nodx;
	export let nody;
	export let xaxis;
	export let yaxis;
	export let tc;
	export let tm;
	console.log(dataProvider)
	
	let max_x ;
  let max_y ;
  let min_x;
  let min_y;
  let max_t;
  let min_t;

	let scale_object;
  
	const { styleable } = getContext("sdk")
	const component = getContext("component")
	import Axis from "./Axis.svelte";
	let dataa = { a: [] };
	
	
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
		  t:xx[tm],
		}
	  });
  
	  if (newData.length > 0) {
		dataa.a = newData;
	  
	  max_x =  dataa.a[0]['x']*1.5;
	  min_x = dataa.a[0]['x']*.9;
	  max_y = dataa.a[0]['y']*(1.5);
	  min_y = dataa.a[0]['y']*(.9);
	  
	  max_t= new Date(new Date(dataa.a[0]['t']).getTime()*1.1);
	  min_t=new Date(new Date(dataa.a[0]['t']).getTime()*0.9);
	  
	  for (let i = 1; i < dataa.a.length; i++) {
		
		max_x = Math.max(max_x, dataa.a[i]['x']);
		min_x = Math.min(min_x, dataa.a[i]['x']);
		max_y = Math.max(max_y, dataa.a[i]['y']);
		min_y = Math.min(min_y, dataa.a[i]['y']);
		
		max_t=Math.max(max_t,new Date(dataa.a[i]['t']).getTime());
		min_t=Math.min(min_t,new Date(dataa.a[i]['t']).getTime());
		
		
	  }
	}
	  scale_object = {
		max_x: max_x,
		max_y: max_y,
		min_x: min_x,
		min_y: min_y,
		max_t:max_t,
		min_t:min_t,
		nodx: nodx,
		nody: nody,
		xaxis: xaxis,
		yaxis: yaxis,
		
	
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
	  min-height:100%;
	  min-width: 100%;
	  margin: 16 16;
	}
  </style>
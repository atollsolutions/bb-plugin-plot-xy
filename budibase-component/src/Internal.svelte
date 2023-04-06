<script>
	import { getContext, createEventDispatcher, onDestroy } from "svelte";
	import { onMount, afterUpdate} from 'svelte';
	import Axis from "./Axis.svelte";
  
	const dispatch = createEventDispatcher();
  
	export let dataProviderdynamic;
	export let nodx;
	export let nody;
	export let xaxis;
	export let yaxis;
	export let interval;
	export let xaxisd;
	export let yaxisd;
	export let tc;
	export let Data;
	export let scaleobject1;
	let Data2 = [];
	let scaleObject;
	let max_x = 10;
  let max_y = 10;
  let min_x = 0;
  let min_y = 0;
  console.log(scaleobject1)
	const { styleable } = getContext("sdk")
	const component = getContext("component")
  
	let intervalID = null;
	
	function updateData() {

		if (dataProviderdynamic) {

  Data2 = dataProviderdynamic.rows.filter(function(xx) {
  if (!xx[xaxisd] || !xx[yaxisd]) {
    return false; // skip
  }
  return true;
}).map(xx => {
    return {
      x: parseFloat(xx[xaxisd]),
      y: parseFloat(xx[yaxisd]),
    };
  });
}
if( Data2.length>0 ){
	  max_x=Data2[0]['x'];
	  min_x=Data2[0]['x'];
	  max_y=Data2[0]['y'];
	  min_y=Data2[0]['y'];
	  for(let i=1;i<Data2.length;i++){
		max_x=Math.max(max_x,Data2[i]['x']);
		min_x=Math.min(min_x,Data2[i]['x']);
		max_y=Math.max(max_y,Data2[i]['y']);
		min_y=Math.min(min_y,Data2[i]['y']);
	  }
	}
	  scaleObject={
		max_x: Math.max(max_x,scaleobject1.max_x),
		max_y: Math.max(max_y,scaleobject1.max_y),
		min_x:Math.min(min_x,scaleobject1.min_x),
		min_y: Math.min(min_y,scaleobject1.min_y),
		nodx: nodx,
		nody: nody,
		xaxis: xaxis,
		yaxis: yaxis,
		xaxisd: xaxisd,
		yaxisd:yaxisd,
		tc:tc
	  };
	  console.log(scaleObject)
	
}
	function onTrigger() {
	  updateData();
	  dispatch("trigger");
	}
  
	$: {
	  if (intervalID) clearInterval(intervalID);
  
	  if (interval > 0) {
		intervalID = setInterval(() => { onTrigger(); }, interval * 1000);
	  }
  
	  updateData();
	}
	afterUpdate(() => {
		updateData();
	});
  </script>
  
  <div class="chart">
	<h1>{tc}</h1>
  
	<Axis scale_ob={scaleObject} points1={Data2} points={Data} />
  
  </div>
  
  <style>
	.chart {
	  width: 100%;
	  height: 100%;
	  
	  
	  margin: 16 16;
	}
  </style>
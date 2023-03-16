<script>
	import { getContext, createEventDispatcher, onDestroy } from "svelte";
	import Internal from "./Internal.svelte";
	import Axis from "./Axis.svelte";
  
	const dispatch = createEventDispatcher();
  
	export let dataProviderdynamic;
	export let nodx;
	export let nody;
	export let xaxis;
	export let yaxis;
	export let interval;
	export let display;
	export let text;
	export let Data;
	export let scaleobject1;
	let Data2 = [];
	let scaleObject;
  
	const { styleable } = getContext("sdk")
	const component = getContext("component")
  
	let intervalID = null;
  
	function updateData() {
		if (dataProviderdynamic) {
  Data2 = dataProviderdynamic.rows.map(xx => {
    return {
      x: parseFloat(xx[xaxis]),
      y: parseFloat(xx[yaxis]),
    };
  });
}
  
	  let max_x=Data2[0]['x'];
	  let min_x=Data2[0]['x'];
	  let max_y=Data2[0]['y'];
	  let min_y=Data2[0]['y'];
	  for(let i=1;i<Data2.length;i++){
		max_x=Math.max(max_x,Data2[i]['x']);
		min_x=Math.min(min_x,Data2[i]['x']);
		max_y=Math.max(max_y,Data2[i]['y']);
		min_y=Math.min(min_y,Data2[i]['y']);
	  }
  
	  scaleObject={
		max_x: Math.max(max_x,scaleobject1.max_x),
		max_y: Math.max(max_y,scaleobject1.max_y),
		min_x:Math.min(min_x,scaleobject1.min_x),
		min_y: Math.min(min_y,scaleobject1.min_y),
		nodx: nodx,
		nody: nody,
		xaxis: xaxis,
		yaxis: yaxis
	  };
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
  </script>
  
  <div class="chart">
	<h1>Canvas</h1>
  
	<Axis scale_ob={scaleObject} points1={Data2} points={Data} />
  
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

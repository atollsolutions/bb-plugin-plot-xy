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
  let dr={};

	const { styleable } = getContext("sdk")
	const component = getContext("component")
  
	let intervalID = null;
	
	function updateData() {

		if (dataProviderdynamic && dataProviderdynamic.rows) {

  Data2 = dataProviderdynamic.rows.filter(function(xx) {
  if (!xx['ID']) {
    return false; // skip
  }
  return true;
}).map(xx => {
    return {
     
	  id:xx.ID
    };
  });
}

if( Data2.length>0 ){
	if(!dr[Data2[0]['id']]){
	dr[Data2[0]['id']]=[]}
		dr[Data2[0]['id']].push(0);
	  
	  for(let i=1;i<Data2.length;i++){
		if(!dr[Data2[i]['id']]) {
        dr[Data2[i]['id']] = [];
    }
    dr[Data2[i]['id']].push(i);
		
	  }
	}
	  scaleObject={
		max_x: scaleobject1.max_x,
		max_y: scaleobject1.max_y,
		min_x:scaleobject1.min_x,
		min_y: scaleobject1.min_y,
		nodx: nodx,
		nody: nody,
		xaxis: xaxis,
		yaxis: yaxis,
		xaxisd: xaxisd,
		yaxisd:yaxisd,
		tc:tc
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
	afterUpdate(() => {
		updateData();
	});
  </script>
  
  <div class="chart">
	<h1>{tc}</h1>
  
	<Axis scale_ob={scaleObject} points1={Data2} points={Data} dr={dr} />
  
  </div>
  
  <style>
	.chart {
	  width: 100%;
	  height: 100%;
	  
	}
  </style>
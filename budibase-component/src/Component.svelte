<script>
  import { getContext } from "svelte"
  import Internal from "./Internal.svelte";
  export let dataProvider;
  export let dataProviderdynamic;
  export let nodx;
  export let nody;
  export let xaxis;
  export let yaxis;
  export let trigger;
  export let interval;
  export let display;
  export let text;
  
  let max_x,max_y,min_x,min_y;
  let scale_object;

  const { styleable } = getContext("sdk")
  const component = getContext("component")
  import Axis from "./Axis.svelte";
  import data from './paths.js';
    
  let dataa = { a: [] };
	console.log(dataProvider)
  $: if (dataProvider) {
    const newData = dataProvider.rows.map(xx => {
      return {
        x: Number(xx[xaxis]),
        y: Number(xx[yaxis]),
      }
    });

    if (newData.length > 0) {
      dataa.a = newData;
    } else {
      dataa = data;
    }

    max_x = dataa.a[0]['x'];
    min_x = dataa.a[0]['x'];
    max_y = dataa.a[0]['y'];
    min_y = dataa.a[0]['y'];
    
    for (let i = 1; i < dataa.a.length; i++) {
      max_x = Math.max(max_x, dataa.a[i]['x']);
      min_x = Math.min(min_x, dataa.a[i]['x']);
      max_y = Math.max(max_y, dataa.a[i]['y']);
      min_y = Math.min(min_y, dataa.a[i]['y']);
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
  } else {
    dataa = data;
  }
</script>

<div class="chart">
  <Internal on:trigger={trigger} interval={interval} {dataProviderdynamic} display={display} text={text} {nodx} {nody} {xaxis} {yaxis} Data={dataa.a} scaleobject1={scale_object}/>
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
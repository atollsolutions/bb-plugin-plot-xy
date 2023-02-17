<script>
    import { createEventDispatcher, onDestroy } from "svelte";
    
    const dispatch = createEventDispatcher();
    export let interval;
    export let display = true;
    export let text = "Interval";
    let intervalID = null;
    if (interval > 0) {
      intervalID = window.setInterval(() => { dispatch("trigger"); }, interval * 1000);
    }
    onDestroy(() => {
      if(intervalID) window.clearInterval(intervalID);
    });
  </script>

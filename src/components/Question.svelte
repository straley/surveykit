<script lang="ts">
	import {
		Button,
	} from "smelte";

  export let text: string = ""
  export let subtext: string = ""
  // export let format: string = ""
  // export let type: string = ""
  export let choices: {
      text: string,
      value: any
    }[] = []
  export let onselect: (selection:any) => void | undefined 
</script>


	<div class="card-title">
    {@html text}
  </div>
	<div class="card-subtitle">
    {@html subtext}
  </div>
	<div class="choices">
    {#if choices && choices.length}
      {#each choices as choice}
        <div class="p-2">
          <Button text on:click={() => {
            if (typeof onselect === "function") {
              onselect(choice.value)
            }
          }}>{choice.text}</Button>
    		</div>
      {/each}
    {/if}
  </div>


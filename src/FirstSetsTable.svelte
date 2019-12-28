<script>
  export let firstSets;
  export let dependencies;
  export let grammar;

  let activeCells = [];

  function onCellClick(l, index, iteration) {
    // TODO refactor
    activeCells = [];
    activeCells.push(`${l}_${index}_${iteration}`);
    if (iteration === 0) {
      return;
    }
    dependencies[l][index].forEach(v => {
      for (let i = 0; i < dependencies[l].length; i++) {
        activeCells.push(`${l}_${i}_${iteration-1}`);        
      }
    });
  }
</script>

<style>
  .selected {
    background-color: #cdcdcd;
  }
</style>

<table class="table is-bordered">
  <tr>
    <th>Rule</th>
    {#each firstSets['S'][0] as rule, index}
      <th>{index}</th>
    {/each}
  </tr>
  {#each Object.keys(grammar) as l}
    {#each grammar[l] as rule, index}
      <tr>
        <td>
          {l} ->
          {#each rule as item}{item.value}&nbsp;{:else}ε{/each}
        </td>
        {#each firstSets[l][index] as set, iteration}
          <td
            on:click={onCellClick(l, index, iteration)}
            class:selected={activeCells.includes(`${l}_${index}_${iteration}`)}>
            {#if set.length}{`{${set}}`}{:else}∅{/if}
          </td>
        {/each}
      </tr>
    {/each}
  {/each}
</table>

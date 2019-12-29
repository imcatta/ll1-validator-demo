<script>
  export let firstSets = {};
  export let dependencies = {};
  export let grammar = {};

  let iterations;
  const nonTerminal = Object.keys(grammar)[0];
  if (nonTerminal) {
    iterations = Array.from(firstSets[nonTerminal][0].keys());
  } else {
    iterations = [];
  }

  let dependencyCells = [];
  let selectedCell;

  function onCellClick(l, index, iteration) {
    selectedCell = `${l}_${index}_${iteration}`;
    dependencyCells = [];

    console.log(dependencies[l][index]);

    if (iteration >= 1) {
      dependencies[l][index].forEach(v => {
        dependencyCells.push(`${v}_${iteration - 1}`);
      });
      console.log(dependencyCells)
    }
  }
</script>

<style>
  .selected {
    background-color: #dedede;
  }
  .dependency {
    background-color: #d2eaff;
  }
</style>

<table class="table is-bordered">
  <tr>
    <th>Rule</th>
    {#each iterations as iteration}
      <th>{iteration}</th>
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
            class:selected={selectedCell === `${l}_${index}_${iteration}`}
            class:dependency={dependencyCells.includes(`${l}_${iteration}`)}>
            {#if set.length}{`{${set}}`}{:else}∅{/if}
          </td>
        {/each}
      </tr>
    {/each}
  {/each}
</table>

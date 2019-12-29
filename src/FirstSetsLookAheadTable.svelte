<script>
  export let firstSets = {};
  export let dependencies = {};
  export let grammar = {};
  export let lookAheads = {};

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
    const newSelection = `${l}_${index}_${iteration}`;
    if (selectedCell === newSelection) {
      selectedCell = undefined;
      dependencyCells = [];
      return;
    }

    selectedCell = newSelection;
    dependencyCells = [];

    if (iteration >= 1) {
      dependencies[l][index].forEach(v => {
        dependencyCells.push(`${v}_${iteration - 1}`);
      });
    }
  }

  console.log(lookAheads);
</script>

<style>
  .separator {
    background-color: rgb(255, 240, 165);
  }
</style>

<table class="table is-bordered">
  <tr>
    <th>Rule</th>
    {#each iterations as iteration}
      <th>{iteration}</th>
    {/each}
    <th class="separator">
    <th>Look aheads</th>
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
        <td class="separator">
        <td>{`{${lookAheads[l][index]}}`}</td>
      </tr>
    {/each}
  {/each}
</table>

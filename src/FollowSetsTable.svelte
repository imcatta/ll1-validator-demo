<script>
  export let followSets = {};
  export let dependencies = {};

  let iterations;
  let dependencyCells = [];
  let selectedCell;

  $: {
    const nonTerminal = Object.keys(followSets)[0];
    if (nonTerminal) {
      iterations = Array.from(followSets[nonTerminal].keys());
    } else {
      iterations = [];
    }
  }

  function onCellClick(l, iteration) {
    const newSelection = `${l}_${iteration}`;
    if (selectedCell === newSelection) {
      selectedCell = undefined;
      dependencyCells = [];
      return;
    }

    selectedCell = newSelection;
    dependencyCells = [];

    if (iteration >= 1) {
      dependencies["follow_nonTerminals"][l].forEach(v => {
        dependencyCells.push(`${v}_${iteration - 1}`);
      });
    }
  }
</script>

<table class="table is-bordered">
  <tr>
    <th>Non terminal</th>
    {#each iterations as iteration}
      <th>{iteration}</th>
    {/each}
  </tr>
  {#each Object.keys(followSets) as l}
    <tr>
      <td>{l}</td>
      {#each followSets[l] as set, iteration}
        <td
          on:click={onCellClick(l, iteration)}
          class:selected={selectedCell === `${l}_${iteration}`}
          class:dependency={dependencyCells.includes(`${l}_${iteration}`)}>
          {#if set.length}{`{${set}}`}{:else}∅{/if}
        </td>
      {/each}
    </tr>
  {/each}
</table>

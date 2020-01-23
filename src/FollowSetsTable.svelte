<script>
  import Set from "./Set.svelte";

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

  function onCellEnter(l, iteration) {
    selectedCell = `${l}_${iteration}`;
    dependencyCells = [];

    if (iteration >= 1) {
      dependencies["follow_nonTerminals"][l].forEach(v => {
        dependencyCells.push(`${v}_${iteration - 1}`);
      });
    }
  }

  function onCellLeave() {
    selectedCell = undefined;
    dependencyCells = [];
  }
</script>

<table class="table is-bordered">
  <tr>
    <th colspan="100%" class="has-text-centered">Follow Sets</th>
  </tr>
  <tr>
    <td class="has-text-weight-medium has-text-grey-dark">Nonterminal</td>
    {#each iterations as iteration}
      <td class="has-text-weight-medium has-text-grey-dark">{iteration}</td>
    {/each}
  </tr>
  {#each Object.keys(followSets) as l}
    <tr>
      <td>{l}</td>
      {#each followSets[l] as set, iteration}
        <td
          on:mouseenter={onCellEnter(l, iteration)}
          on:mouseleave={onCellLeave}
          class:selected={selectedCell === `${l}_${iteration}`}
          class:dependency={dependencyCells.includes(`${l}_${iteration}`)}>
          <Set {set} />
        </td>
      {/each}
    </tr>
  {/each}
</table>

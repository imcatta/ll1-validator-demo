<script>
  import Set from "./Set.svelte";

  export let firstSets = {};
  export let dependencies = {};
  export let grammar = {};
  export let lookAheads = {};
  export let conflicts = {};

  let iterations;
  let dependencyCells = [];
  let selectedCell;
  let confArray = {};

  $: {
    const nonTerminal = Object.keys(grammar)[0];
    if (nonTerminal) {
      iterations = Array.from(firstSets[nonTerminal][0].keys());
    } else {
      iterations = [];
    }
    Object.keys(lookAheads).forEach(l => {
      confArray[l] = [];
      lookAheads[l].forEach((r, index) => {
        r.forEach(nt => {
          if (conflicts[l].includes(nt)) {
            confArray[l][index] = true;
          }
        });
      });
    });
  }

  function onCellEnter(l, index, iteration) {
    selectedCell = `${l}_${index}_${iteration}`;
    dependencyCells = [];

    if (iteration >= 1) {
      dependencies[l][index].forEach(v => {
        dependencyCells.push(`${v}_${iteration - 1}`);
      });
    }
  }

  function onCellLeave() {
    selectedCell = undefined;
    dependencyCells = [];
  }
</script>

<style>
  .separator {
    background-color: #efefef;
  }
  .conflict {
    background-color: #fdc3c3;
  }
</style>

<table class="table is-bordered">
  <tr>
    <th>Rule</th>
    {#each iterations as iteration}
      <th>{iteration}</th>
    {/each}
    <th class="separator" />
    <th>Look Aheads</th>
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
            on:mouseenter={onCellEnter(l, index, iteration)}
            on:mouseleave={onCellLeave}
            class:selected={selectedCell === `${l}_${index}_${iteration}`}
            class:dependency={dependencyCells.includes(`${l}_${iteration}`)}>
            <Set {set} />
          </td>
        {/each}
        <td class="separator" />
        {#if confArray[l][index] === true}
          <td class="conflict">
            <Set set={lookAheads[l][index]} />
          </td>
        {:else}
          <td>
            <Set set={lookAheads[l][index]} />
          </td>
        {/if}
      </tr>
    {/each}
  {/each}
</table>

<script>
  import Set from "./Set.svelte";
  import Rule from "./Rule.svelte";

  export let firstSets = {};
  export let dependencies = {};
  export let grammar = {};
  export let lookAheads = {};
  export let lookAheadsConflicts = {};
  export let warnings = [];

  let iterations;
  let dependencyCells = [];
  let selectedCell;
  let confArray = {};
  let selectedRule;

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
          if (lookAheadsConflicts[l].includes(nt)) {
            confArray[l][index] = true;
          }
        });
      });
    });
  }

  function onCellEnter(l, index, iteration) {
    selectedCell = `${l}#${index}#${iteration}`;
    dependencyCells = [];

    if (iteration >= 1) {
      dependencies[l][index].forEach(v => {
        dependencyCells.push(`${v}#${iteration - 1}`);
      });
    }
  }

  function onCellLeave() {
    selectedCell = undefined;
    dependencyCells = [];
  }

  function getDuplicateRuleWarningMessage(l, index) {
    const warn = warnings.find(
      v =>
        v.nonTerminal === l &&
        v.index === index &&
        v.constructor.name === "DuplicatedRuleWarning"
    );
    return warn ? warn.message : undefined;
  }

  function getUnreachableRuleWarningMessage(l) {
    const warn = warnings.find(
      v =>
        v.nonTerminal === l && v.constructor.name === "UnreachableRuleWarning"
    );
    return warn ? warn.message : undefined;
  }

  function onWarnMouseEnter(l, index) {
    console.log("onWarnMouseEnter");
    const warn = warnings.find(
      v =>
        v.nonTerminal === l &&
        v.index === index &&
        v.constructor.name === "DuplicatedRuleWarning"
    );
    selectedRule = `${warn.nonTerminal}#${warn.sameAs}`;
    console.log(selectedRule);
  }

  function onWarnMouseLeave() {
    selectedRule = undefined;
  }
</script>

<style>
  .separator {
    background-color: #efefef;
  }
  .conflict {
    background-color: #fdc3c3;
  }
  .selected-rule {
    background-color: #fdf9c3;
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
        <td class:selected-rule={selectedRule === `${l}#${index}`}>
          <Rule
            {l}
            {rule}
            {index}
            duplicatedRuleWarningMessage={getDuplicateRuleWarningMessage(l, index)}
            unreachableRuleWarningMessage={getUnreachableRuleWarningMessage(l)}
            on:duplicatedRuleWarningMouseEnter={() => onWarnMouseEnter(l, index)}
            on:duplicatedRuleWarningMouseLeave={() => onWarnMouseLeave()} />
        </td>
        {#each firstSets[l][index] as set, iteration}
          <td
            on:mouseenter={onCellEnter(l, index, iteration)}
            on:mouseleave={onCellLeave}
            class:selected={selectedCell === `${l}#${index}#${iteration}`}
            class:dependency={dependencyCells.includes(`${l}#${iteration}`)}>
            <Set {set} />
          </td>
        {/each}
        <td class="separator" />
        <td class:conflict={confArray[l][index]}>
          <Set set={lookAheads[l][index]} />
        </td>
      </tr>
    {/each}
  {/each}
</table>

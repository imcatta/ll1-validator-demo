<script>
  import Set from "./Set.svelte";
  export let isLL1;
  export let terminals;
  export let nonTerminals;
  export let nullableNonTerminals;
  let nullables;

  $: {
    nullables = [];
    for (const nonTerminal in nullableNonTerminals) {
      if (nullableNonTerminals[nonTerminal]) {
        nullables.push(nonTerminal);
      }
    }
  }
</script>

<table class="table is-bordered">
  <tr>
    <th>LL(1)</th>
    <td>
      <span
        class="icon has-tooltip-right"
        class:has-text-success={isLL1}
        class:has-text-danger={!isLL1}
        data-tooltip={isLL1 ? 'The grammar is LL(1)' : 'The grammar is not LL(1)'}>
        <i
          class="fas fa-lg"
          class:fa-check-circle={isLL1}
          class:fa-times-circle={!isLL1} />
      </span>
    </td>
  </tr>
  <tr>
    <th>Terminals</th>
    <td>
      <Set set={terminals} />
    </td>
  </tr>
  <tr>
    <th>Nonterminals</th>
    <td>
      <Set set={nonTerminals} />
    </td>
  </tr>
  <tr>
    <th>Nullables</th>
    <td>
      <Set set={nullables} />
    </td>
  </tr>
</table>

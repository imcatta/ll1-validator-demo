<script>
  import Set from "./Set.svelte";
  export let resultText;
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

<div class="content">
  <h2>{resultText}</h2>
</div>
<hr />
<table class="table is-bordered">
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

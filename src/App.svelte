<script>
  import FirstSetsTable from "./FirstSetsTable.svelte";
  import { parser, ll1 } from "ll1-validator";

  let grammarString = "S -> A;\nA -> A a;\nA -> A b a;\nA -> Z;\nA -> ;\nZ -> Z x;\nZ -> Z x y;\nZ -> S;";
  let grammar;
  let firstSets;
  let dependencies;

  function calculate() {
    grammar = parser.parseString(grammarString);
    firstSets = ll1.calculateFirstSets(grammar);
    dependencies = ll1.calculateFirstSetsDependencies(grammar);
  }
  calculate();
</script>

<style>
  :global(body) {
    background-color: #efefef;
  }
  .textarea {
    margin-bottom: 5px;
  }
  .container {
    padding-top: 5px;
  }
</style>

<div class="container">
  <h1 class="title is-2">LL(1) validator</h1>
  <div class="columns">
    <div class="column is-one-third">
      <div class="box">
        <textarea rows="15" class="textarea" bind:value={grammarString} />
        <button class="button is-primary" on:click={calculate}>
          Calculate
        </button>
      </div>
    </div>
    <div class="column">
      <div class="box">
        {#if grammar}
          <FirstSetsTable {grammar} {firstSets} {dependencies} />
        {/if}
      </div>
    </div>
  </div>
</div>

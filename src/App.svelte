<script>
  import FirstSetsLookAheadTable from "./FirstSetsLookAheadTable.svelte";
  import FollowSetsTable from "./FollowSetsTable.svelte";
  import { parser, ll1 } from "ll1-validator";

  let grammarString =
    "S -> A;\nA -> A a;\nA -> A b a;\nA -> Z;\nA -> ;\nZ -> Z x;\nZ -> Z y x;\nZ -> S;";
  let grammar;
  let firstSets;
  let followSets;
  let firstSetsDependencies;
  let followSetsDependencies;
  let lookAheads;

  function calculate() {
    grammar = parser.parseString(grammarString);
    firstSets = ll1.calculateFirstSets(grammar);
    followSets = ll1.calculateFollowSets(grammar);
    firstSetsDependencies = ll1.calculateFirstSetsDependencies(grammar);
    followSetsDependencies = ll1.calculateFollowSetDipendencies(grammar);
    lookAheads = ll1.calculateLookAheads(grammar);
  }
  calculate();
</script>

<style>
  :global(html) {
    background-color: #efefef !important;
  }

  :global(.selected) {
    background-color: #dedede;
  }
  :global(.dependency) {
    background-color: #d2eaff;
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
          <FirstSetsLookAheadTable
            {grammar}
            {firstSets}
            dependencies={firstSetsDependencies}
            {lookAheads} />
          <FollowSetsTable {followSets} dependencies={followSetsDependencies} />
        {/if}
      </div>
    </div>
  </div>
</div>

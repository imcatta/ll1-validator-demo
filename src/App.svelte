<script>
  import FirstSetsLookAheadTable from "./FirstSetsLookAheadTable.svelte";
  import FollowSetsTable from "./FollowSetsTable.svelte";
  import { parser, ll1 } from "ll1-validator";

  let grammarString =
    "/* this is the grammar \n" +
    "used to parse the input */\n" +
    "_start_symbol S; // optional, 'S' by default\n\n" +
    "S -> SS RULE RULELIST;\n" +
    "SS -> ssk nt semicolon;\n" +
    "SS -> ;\n" +
    "RULELIST -> RULE RULELIST;\n" +
    "RULELIST -> ;\n" +
    "RULE -> L assign R semicolon;\n" +
    "L -> nt;\n" +
    "R -> nt R;\n" +
    "R -> t R;\n" +
    "R -> ; // you can also use inline comments\n";
  let grammar;
  let firstSets;
  let followSets;
  let firstSetsDependencies;
  let followSetsDependencies;
  let lookAheads;
  let axiom;

  function calculate() {
    grammar = parser.parseString(grammarString);
    axiom=grammar._start_symbol;
    firstSets = ll1.calculateFirstSets(grammar);
    followSets = ll1.calculateFollowSets(grammar);
    firstSetsDependencies = ll1.calculateFirstSetsDependencies(grammar);
    followSetsDependencies = ll1.calculateFollowSetDipendencies(grammar,axiom);
    lookAheads = ll1.calculateLookAheads(grammar);
    delete grammar._start_symbol;
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
  .column,
  .box {
    overflow-x: auto;
  }
  .title {
    margin-top: 4px;
    margin-left: 8px;
  }
</style>

<div class="container">
  <h1 class="title is-2">LL(1) Validator</h1>
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

<script>
  import FirstSetsLookAheadTable from "./FirstSetsLookAheadTable.svelte";
  import FollowSetsTable from "./FollowSetsTable.svelte";
  import { parser, ll1 } from "ll1-validator";

  let grammarString =
    "/* this is the grammar \n" +
    "used to parse the input */\n" +
    "_start_symbol S; // optional\n\n" +
    "S -> SS RULE RULELIST;\n" +
    "SS -> ssk nt semicolon;\n" +
    "SS -> ;\n" +
    "RULELIST -> RULE RULELIST;\n" +
    "RULELIST -> ;\n" +
    "RULE -> L assign R semicolon;\n" +
    "L -> nt;\n" +
    "R -> nt R;\n" +
    "R -> t R;\n" +
    "R -> ;\n";
  let grammar;
  let firstSets;
  let followSets;
  let firstSetsDependencies;
  let followSetsDependencies;
  let lookAheads;
  let axiom;
  let isLL1;
  let conflicts;
  let resulttxt;
  let errorMessage;

  function clearVariables() {
    grammar = undefined;
    firstSets = undefined;
    followSets = undefined;
    firstSetsDependencies = undefined;
    followSetsDependencies = undefined;
    lookAheads = undefined;
    axiom = undefined;
    isLL1 = undefined;
    conflicts = undefined;
    resulttxt = undefined;
    errorMessage = undefined;
  }

  function calculate() {
    clearVariables();

    try {
      grammar = parser.parseString(grammarString);
      axiom = grammar._start_symbol;
      firstSets = ll1.calculateFirstSets(grammar);
      followSets = ll1.calculateFollowSets(grammar);
      firstSetsDependencies = ll1.calculateFirstSetsDependencies(grammar);
      followSetsDependencies = ll1.calculateFollowSetDependencies(
        grammar,
        axiom
      );
      lookAheads = ll1.calculateLookAheads(grammar);
      isLL1 = ll1.isLL1(grammar);
      conflicts = ll1.calculateAllConflicts(grammar);
    } catch (e) {
      errorMessage = e.message;
      clearVariables();
      return;
    }

    delete grammar._start_symbol;

    if (isLL1 != undefined) {
      if (isLL1) resulttxt = "The grammar is LL1";
      else if (!isLL1) resulttxt = "The grammar is not LL1";
    }
  }
  calculate();
</script>

<style>
  :global(html) {
    background-color: #efefef !important;
  }
  :global(.selected) {
    background-color: #e5f8d5;
  }
  :global(.dependency) {
    background-color: #d9e9f6;
  }
  .textarea {
    margin-bottom: 5px;
    font-family: monospace;
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
  .help {
    border-left: solid 4px;
    padding-left: 5px;
    margin-bottom: 5px;
    font-size: 13px;
    font-family: monospace;
  }
</style>

<div class="container">
  <h1 class="title is-2">LL(1) Validator</h1>
  <div class="columns">
    <div class="column is-one-third">
      <div class="box">
        <textarea rows="15" class="textarea" bind:value={grammarString} />
        {#if errorMessage}
          <p class="help is-danger mb-2">{errorMessage}</p>
        {/if}
        <button class="button is-primary" on:click={calculate}>
          Calculate
        </button>
      </div>
      {#if resulttxt}
        <div class="content is-small">
          <h2>{resulttxt}</h2>
        </div>
      {/if}
    </div>
    <div class="column">
      {#if grammar}
        <div class="box">
          <FirstSetsLookAheadTable
            {grammar}
            {firstSets}
            dependencies={firstSetsDependencies}
            {lookAheads}
            {conflicts} />
          <FollowSetsTable {followSets} dependencies={followSetsDependencies} />
        </div>
      {/if}
    </div>
  </div>
</div>

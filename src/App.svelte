<script>
  import FirstSetsLookAheadTable from "./FirstSetsLookAheadTable.svelte";
  import FollowSetsTable from "./FollowSetsTable.svelte";
  import GrammarInformation from "./GrammarInformation.svelte";
  import { validate } from "ll1-validator";

  let grammar;
  let startSymbol;
  let rulesNumber;
  let nullableNonTerminals;
  let terminals;
  let nonTerminals;
  let warnings;
  let firstSets;
  let followSets;
  let firstSetsDependencies;
  let followSetsDependencies;
  let lookAheads;
  let isLL1;
  let lookAheadsConflicts;
  let errorMessage;
  let grammarString =
    "/* this is the grammar \n" +
    "used to parse the input */\n" +
    "#start_symbol S; // optional\n\n" +
    "S -> SS RULE RULELIST;\n" +
    "SS -> ssk sym semicolon;\n" +
    "SS -> ;\n" +
    "RULELIST -> RULE RULELIST;\n" +
    "RULELIST -> ;\n" +
    "RULE -> L assign R semicolon;\n" +
    "L -> sym;\n" +
    "R -> sym R;\n" +
    "R -> ;\n";

  function clearVariables() {
    grammar = undefined;
    startSymbol = undefined;
    rulesNumber = undefined;
    nullableNonTerminals = undefined;
    terminals = undefined;
    warnings = undefined;
    nonTerminals = undefined;
    firstSets = undefined;
    followSets = undefined;
    firstSetsDependencies = undefined;
    followSetsDependencies = undefined;
    lookAheads = undefined;
    isLL1 = undefined;
    lookAheadsConflicts = undefined;
  }

  function calculate() {
    try {
      ({
        grammar,
        startSymbol,
        rulesNumber,
        nullableNonTerminals,
        terminals,
        nonTerminals,
        warnings,
        firstSets,
        followSets,
        firstSetsDependencies,
        followSetsDependencies,
        lookAheads,
        isLL1,
        lookAheadsConflicts
      } = validate(grammarString));
      errorMessage = undefined;
    } catch (e) {
      clearVariables();
      errorMessage = e.message;
    }
  }
  calculate();
</script>

<style>
  @import "../node_modules/bulma/css/bulma.min.css";
  @import "../node_modules/bulma-tooltip/dist/css/bulma-tooltip.min.css";

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
        A web application based on
        <a target="_blank" href="https://github.com/imcatta/ll1-validator">ll1-validator</a>
        .
      </div>
      <div class="box">
        <textarea
          rows="15"
          class="textarea"
          autocomplete="off"
          autocorrect="off"
          autocapitalize="off"
          spellcheck="false"
          bind:value={grammarString} />
        {#if errorMessage}
          <p class="help is-danger mb-2">{errorMessage}</p>
        {/if}
        <a href="#" target="_blank">Need help?</a>
        <button class="button is-primary is-pulled-right" on:click={calculate}>
          Calculate
        </button>
      </div>
      {#if grammar}
        <div class="box">
          <GrammarInformation
            {isLL1}
            {terminals}
            {nonTerminals}
            {nullableNonTerminals} />
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
            {lookAheadsConflicts}
            {warnings} />
          <FollowSetsTable {followSets} dependencies={followSetsDependencies} />
        </div>
      {/if}
    </div>
  </div>
</div>

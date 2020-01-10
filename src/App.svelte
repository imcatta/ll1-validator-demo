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
  let isLL1;
  let conflicts;
  let resulttxt;

  function cleanVariables(){
    grammar=undefined;
    firstSets={};
    followSets={};
    firstSetsDependencies={};
    followSetsDependencies={};
    lookAheads={};
    axiom="";
    isLL1=undefined;
    conflicts={};
  }

  function checkGrammar(){
    let arrLines= grammarString.match(/[^\r\n]+/g);
    let grammarOk=true;
    let commentLine=false
    arrLines.forEach((l,index) =>{
      if(grammarOk){
        l= l.replace(/\s/g, '');
        if(l.includes("/*")){
          commentLine=true;
        }
        else if (l.includes("*/")){
          if(commentLine)
            commentLine=false;
          else{
            grammarOk=false;
            resulttxt= " Tried to close a comment section that didn't start anywhere at line "+(index+1);
          }
        }
        else{
          if (!commentLine)
          {
            l= l.split("//")[0]
            if(l.length>0 && !(l[l.length-1]===";")){
              grammarOk=false;
              resulttxt="Missing ; in line "+(index+1);
            }
          }
        }
      }
    });
    return grammarOk;
  }

  function calculate() {
    resulttxt=""
    cleanVariables();
    if(checkGrammar()){
    try{
      grammar = parser.parseString(grammarString);
      }
    catch (e)
    {
      cleanVariables();
      resulttxt="Error while parsing the grammar. Please check carefully"
      console.log(e)
    }
    if(grammar){
      try{
        axiom=grammar._start_symbol;
        firstSets = ll1.calculateFirstSets(grammar);
        followSets = ll1.calculateFollowSets(grammar);
        firstSetsDependencies = ll1.calculateFirstSetsDependencies(grammar);
        followSetsDependencies = ll1.calculateFollowSetDipendencies(grammar,axiom);
        lookAheads = ll1.calculateLookAheads(grammar);
        isLL1=ll1.isLL1(grammar);
        conflicts=ll1.calculateAllConflicts(grammar);
      }
      catch(err)
      {
        cleanVariables();
        resulttxt="Error while calculating LL1. If the error persist, please contact the developers."
        console.log(err)
      }

    }
    delete grammar._start_symbol;
    }
    else
    {
      cleanVariables();
    }
    if(isLL1!=undefined)
    {
      if(isLL1)
        resulttxt="The grammar is LL1"
      else if (!isLL1)
        resulttxt="The grammar is not LL1"
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
      <div class="content is-small">
        <h2>
          {resulttxt}
        </h2>
      </div>
    </div>
    <div class="column">
      <div class="box">
        {#if grammar}
          <FirstSetsLookAheadTable
            {grammar}
            {firstSets}
            dependencies={firstSetsDependencies}
            {lookAheads}
            {conflicts} />
          <FollowSetsTable {followSets} dependencies={followSetsDependencies} />         
        {/if}
      </div>
    </div>
  </div>
</div>

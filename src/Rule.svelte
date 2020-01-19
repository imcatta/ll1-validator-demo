<script>
  export let l;
  export let rule;
  export let index;
  export let warnings;

  let warningMessage;

  $: {
    const warning = warnings.find(
      v => v.nonTerminal === l && v.index === index
    );
    if (warning) {
      warningMessage = warning.message;
    } else {
      warningMessage = undefined;
    }
  }
</script>

<style>
  .icon {
    margin-right: 4px;
  }
</style>

{#if warningMessage}
  <span
    class="icon has-text-warning is-pulled-right has-tooltip-right"
    data-tooltip={warningMessage}>
    <i class="fas fa-exclamation-triangle fa-lg" />
  </span>
{/if}
{l} ->
{#each rule as item}{item.value}&nbsp;{:else}ε{/each}

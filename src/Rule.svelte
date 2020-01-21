<script>
  export let l;
  export let rule;
  export let index;
  export let warnings;

  let duplicatedRuleWarningMessage;
  let unreachableRuleWarningMessage;

  $: {
    duplicatedRuleWarningMessage = undefined;
    unreachableRuleWarningMessage = undefined;

    warnings
      .filter(v => v.nonTerminal === l)
      .forEach(warning => {
        switch (warning.constructor.name) {
          case "DuplicatedRuleWarning":
            if (warning.index === index) {
              duplicatedRuleWarningMessage = warning.message;
            }
            break;
          case "UnreachableRuleWarning":
            unreachableRuleWarningMessage = warning.message;
            break;
        }
      });
  }
</script>

<style>
  .icon {
    margin-right: 4px;
  }
</style>

{#if unreachableRuleWarningMessage}
  <span
    class="icon has-text-info is-pulled-right has-tooltip-right"
    data-tooltip={unreachableRuleWarningMessage}>
    <i class="fas fa-info-circle fa-lg" />
  </span>
{/if}
{#if duplicatedRuleWarningMessage}
  <span
    class="icon has-text-warning is-pulled-right has-tooltip-right"
    data-tooltip={duplicatedRuleWarningMessage}>
    <i class="fas fa-exclamation-triangle fa-lg" />
  </span>
{/if}
{l} ->
{#each rule as item}{item.value}&nbsp;{:else}ε{/each}

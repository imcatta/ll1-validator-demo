<script>
  import { createEventDispatcher } from "svelte";

  export let l;
  export let rule;
  export let duplicatedRuleWarningMessage;
  export let unreachableRuleWarningMessage;

  const dispatch = createEventDispatcher();
</script>

<style>
  .icon {
    margin-left: 5px;
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
    data-tooltip={duplicatedRuleWarningMessage}
    on:mouseenter={() => dispatch('duplicatedRuleWarningMouseEnter')}
    on:mouseleave={() => dispatch('duplicatedRuleWarningMouseLeave')}>
    <i class="fas fa-exclamation-triangle fa-lg" />
  </span>
{/if}
{l} ->
{#each rule as item}{item.value}&nbsp;{:else}ε{/each}

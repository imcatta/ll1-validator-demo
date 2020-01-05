<script>
  export let set = [];
</script>

{#if set.length}{`{${set.join(', ')}}`}{:else}∅{/if}

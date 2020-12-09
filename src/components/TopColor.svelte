<script>
  export let color;

  $: letters = color ? color.letters : null;
  $: hexCode = color ? color.hex : "";
</script>

<div class="top-color--container" style="background: {hexCode}">
  <div class="top-color--hex">{hexCode}</div>
</div>

<p class="top-color--name">
  {#if !letters}
    <span>--</span>
  {:else}
    {#each letters as letter}
      <span class="{letter.wasFound ? "was-found" : ""}">{letter.letter.toUpperCase()}</span>
    {/each}
  {/if}
</p>

<style type="text/scss">
  .top-color {
    &--container {
      width: 300px;
      height: 300px;
      margin: auto;
      border-radius: 3px;
      background: black;
      box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
      position: relative;
    }

    &--hex {
      position: absolute;
      left: 50%;
      top: 0px;
      transform: translateX(-50%);
      text-transform: uppercase;
      background: rgba(28, 28, 28, .5);
      font-weight: 400;
      padding: 2px 6px;
    }

    &--name {
      text-align: center;
      font-size: 20px;
    }
  }
</style>

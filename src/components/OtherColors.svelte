<script>
  import { fly } from 'svelte-transitions';

  export let color;
  let highlight;

  function toggleHighlight() {
    highlight = !highlight;
  }

</script>

<div transition:fly class="other-color--container" on:mouseenter={toggleHighlight} on:mouseleave={toggleHighlight}>
  <div class="other-color--color {highlight ? "highlight" : ""}" style="background: {color.hex}">
    <div class="other-color--hex">{color.hex}</div>
  </div>
  <p>
    {#each color.letters as letter}
      <span class="{letter.wasFound ? "was-found" : ""}">{letter.letter.toUpperCase()}</span>
    {/each}
  </p>
</div>

<style type="text/scss">
  .other-color {
    &--container {
      width: calc(10% - 20px);
      min-width: 100px;
      text-align: center;
      padding: 0px 10px;
    }

    &--color {
      width: 50px;
      height: 50px;
      margin: auto;
      box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
      border-radius: 3px;
      position: relative;
      transition: 250ms;

      &.highlight {
        width: 60px;
        height: 60px;

        .other-color--hex {
          opacity: 1;
        }        
      }
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
      font-size: 10px;
      width: 100%;
      opacity: 0;
      transition: 250ms;
    }

    &--name {
      font-size: 14px;
      width: 100%;
    }
  }
</style>
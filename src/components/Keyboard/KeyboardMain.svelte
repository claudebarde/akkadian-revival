<script>
  import ClassicKeyboard from "./Views/ClassicKeyboard.svelte";
  import AdvancedKeyboard from "./Views/AdvancedKeyboard.svelte";
  import store from "./store.js";
</script>

<style>
  .navigation {
    display: flex;
    flex-direction: row;
    justify-content: center;
  }
</style>

<main>
  <div class="column is-three-fifths is-offset-one-fifth has-text-centered">
    <h1 class="title">Akkadian Keyboard</h1>
    <h3 class="subtitle is-capitalized">{$store.view}</h3>
    <div class="buttons navigation">
      <button
        class="button is-link is-light"
        on:click={() => store.changeView('classic')}>
        Classic
      </button>
      <button
        class="button is-warning is-light"
        on:click={() => store.changeView('advanced')}>
        Advanced
      </button>
    </div>
  </div>
  {#if $store.view === 'classic'}
    <ClassicKeyboard />
  {:else if $store.view === 'advanced'}
    <AdvancedKeyboard />
  {/if}
</main>

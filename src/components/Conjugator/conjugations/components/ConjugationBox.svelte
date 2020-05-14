<script>
  import state from "../../state/state";

  import highlightRoot from "../../settings/highlightRoot.js";
  import settings from "../../settings/settings.js";
  import addVentive from "../../settings/addVentive";

  export let verb;
  export let title;
  export let conjugation;
  export let vetitive = false;

  const personsSing = ["3cs", "2ms", "2fs", "1cs"];
  const personsPlur = ["3mp", "3fp", "2cp", "1cp"];

  const displayInfo = (verb, person) => {
    state.updateInfoModal({ open: true, verb, person });
  };

  const insertVerb = (verb, ps) => {
    return highlightRoot({
      verb: $state.ventive
        ? addVentive({
            verb,
            ps,
            conjugation,
            root: $state.root,
            infinitive: $state.infinitive
          })
        : verb,
      root: $state.root,
      conjugation,
      ps,
      infinitive: $state.infinitive,
      ventive: $state.ventive,
      verbClass: $state.verbClass,
      vetitive
    });
  };

  const insertVentive = (verb, ps) => {
    if ($state.ventive) {
      return addVentive({
        verb,
        ps,
        conjugation,
        root: $state.root,
        infinitive: $state.infinitive
      });
    } else {
      return verb;
    }
  };
</script>

<style>
  .verbDisplay {
    background-color: transparent;
  }
</style>

<!-- MOBILE VERSION -->
<div class="message is-primary is-small is-hidden-tablet conjugation-box">
  <div class="message-header">
    <p>{title}</p>
  </div>
  <div class="message-body">
    <div class="columns is-mobile">
      <div class="column is-half">
        <table class="table is-narrow verbDisplay">
          <tbody>
            {#each personsSing as ps}
              <tr
                style="cursor:pointer;"
                on:click={() => displayInfo(insertVentive(verb[ps], ps), ps)}>
                <td>
                  <span class="has-text-grey-light is-size-7">{ps}:</span>
                </td>
                <td>
                  {#if $state.rootHighlight}
                    <span class="has-text-black">
                      {@html insertVerb(verb[ps], ps)}
                    </span>
                  {:else}
                    <span class="has-text-black">
                      {@html insertVentive(verb[ps], ps)}
                    </span>
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
      <div class="column is-half">
        <table class="table is-narrow verbDisplay">
          <tbody>
            {#each personsPlur as ps}
              <tr
                style="cursor:pointer;"
                on:click={() => displayInfo(insertVentive(verb[ps], ps), ps)}>
                <td>
                  <span class="has-text-grey-light is-size-7">{ps}:</span>
                </td>
                <td>
                  {#if $state.rootHighlight}
                    <span class="has-text-black">
                      {@html insertVerb(verb[ps], ps)}
                    </span>
                  {:else}
                    <span class="has-text-black">
                      {@html insertVentive(verb[ps], ps)}
                    </span>
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>

<!-- COMPUTER VERSION -->
<div class="message is-primary is-hidden-mobile conjugation-box">
  <div class="message-header">
    <p>{title}</p>
  </div>
  <div class="message-body">
    <div class="columns is-mobile">
      <div class="column is-half">
        <table class="table is-narrow verbDisplay">
          <tbody>
            {#each personsSing as ps}
              <tr
                style="cursor:pointer;"
                on:click={() => displayInfo(insertVentive(verb[ps], ps), ps)}>
                <td>
                  <span class="has-text-grey-light is-size-7">{ps}:</span>
                </td>
                <td>
                  {#if $state.rootHighlight}
                    <span class="has-text-black">
                      {@html insertVerb(verb[ps], ps)}
                    </span>
                  {:else}
                    <span class="has-text-black">
                      {@html insertVentive(verb[ps], ps)}
                    </span>
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
      <div class="column is-half">
        <table class="table is-narrow verbDisplay">
          <tbody>
            {#each personsPlur as ps}
              <tr
                style="cursor:pointer;"
                on:click={() => displayInfo(insertVentive(verb[ps], ps), ps)}>
                <td>
                  <span class="has-text-grey-light is-size-7">{ps}:</span>
                </td>
                <td>
                  {#if $state.rootHighlight}
                    <span class="has-text-black">
                      {@html insertVerb(verb[ps], ps)}
                    </span>
                  {:else}
                    <span class="has-text-black">
                      {@html insertVentive(verb[ps], ps)}
                    </span>
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>

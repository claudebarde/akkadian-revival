<script>
  import { createEventDispatcher, onMount } from "svelte";
  import lexicon from "../lexicon/lexicon.js";
  import state from "../state/state";

  const dispatch = createEventDispatcher();

  let openVerbsList = false;
  let verbs = {};
  $: verbType = "allverbs";
  const letters = [];
  const alphabet = [
    "A",
    "B",
    "D",
    "E",
    "Ê",
    "G",
    "Ḫ",
    "I",
    "K",
    "L",
    "M",
    "N",
    "P",
    "Q",
    "R",
    "S",
    "Š",
    "Ṣ",
    "T",
    "Ṭ",
    "U",
    "W",
    "Z"
  ];

  const createVerbsList = () => {
    let verbs = {};
    let newList = [];
    if (verbType === "allverbs") {
      newList = Object.keys(lexicon);
    } else if (verbType === "Iweak") {
      newList = Object.keys(lexicon).filter(el => lexicon[el].root[0] === "Ø");
    } else if (verbType === "IIweak") {
      newList = Object.keys(lexicon).filter(el => lexicon[el].root[1] === "Ø");
    } else if (verbType === "IIIweak") {
      newList = Object.keys(lexicon).filter(el => lexicon[el].root[2] === "Ø");
    }

    newList.forEach(verb => {
      const initial = verb[0].toUpperCase();
      let str = `${verb} <span class="is-size-7">(${
        lexicon[verb].durativeVowel ? lexicon[verb].durativeVowel + " - " : ""
      }${lexicon[verb].themeVowel || "-"})</span>`;
      if (!verbs.hasOwnProperty(initial)) {
        verbs[initial] = [str];
      } else {
        str = verbs[initial] = [...verbs[initial], str];
      }
    });

    return verbs;
  };

  const selectNewVerb = item => {
    if (item !== $state.infinitive) {
      dispatch("selectVerb", { verb: item, details: lexicon[item] });
      // removes all active elements
      const els = document.getElementsByClassName("is-active");
      while (els[0]) {
        els[0].classList.remove("is-active");
      }
      // adds new active element
      document.getElementById(item).className += " is-active";
    }
  };

  onMount(() => {
    verbs = createVerbsList(lexicon);
  });
</script>

<style>
  .verbslist {
    position: fixed;
    bottom: 0px;
    left: 0px;
  }

  .verbslist__content {
    overflow: auto;
    height: 95vh;
    padding-top: 80px;
  }
</style>

<div
  class="dropdown is-up verbslist is-hidden-desktop"
  class:is-active={openVerbsList}
  style="width:100%">
  <div class="dropdown-trigger" style="width:100%">
    <button
      class="button is-fullwidth is-outlined"
      aria-haspopup="true"
      aria-controls="dropdown-menu"
      on:click={() => (openVerbsList = !openVerbsList)}>
      <span>Verbs List</span>
      <span class="icon is-small">
        <i class="fas fa-angle-up" aria-hidden="true" />
      </span>
    </button>
  </div>
  <div class="dropdown-menu" id="dropdown-menu" role="menu">
    <div class="dropdown-content verbslist__content">
      {#each alphabet as letter}
        <div class="dropdown-item">
          {#if verbs[letter]}
            <p class="has-text-grey-light">{letter}</p>
          {/if}
          {#if verbs[letter]}
            <div class="columns is-mobile is-multiline">
              {#each verbs[letter].sort(Intl.Collator().compare) as verb}
                <div
                  on:click|preventDefault={() => {
                    openVerbsList = false;
                    selectNewVerb(verb.split(' ')[0]);
                  }}
                  class="column is-half has-text-centered menuItem">
                  <a href="#/" id={verb.split(' ')[0]}>
                    {@html verb}
                  </a>
                </div>
              {/each}
            </div>
          {/if}
        </div>
        <hr class="dropdown-divider" />
      {/each}
    </div>
  </div>
</div>

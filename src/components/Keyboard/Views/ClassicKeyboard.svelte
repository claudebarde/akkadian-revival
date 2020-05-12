<script>
  import insertTextAtCursor from "insert-text-at-cursor";
  import { onMount } from "svelte";
  import { push } from "svelte-spa-router";
  import syllableParser from "../utils/syllableParser.js";
  import cuneiformsGenerator from "../utils/cuneiformsGenerator.js";
  import dictionarySearch from "../utils/dictionarySearch.js";
  import stressMarker from "../utils/stressMarker.js";
  import store from "../store.js";
  import CuneiformWord from "../CuneiformWord.svelte";
  import RawCuneiforms from "../RawCuneiforms.svelte";
  import dictionary from "../../../databases/dictionary.json";
  import { monoconsonants as syllabary } from "../../../databases/syllabary.json";

  let newLinesPos = [];
  let textareaRef;
  let selectedTab = "detailed";

  $: if ($store.input.length === 0) {
    newLinesPos = [];
    store.resetWords();
  }

  const processInput = event => {
    let parsedSyllables, cuneiforms;
    let text = event.target.value;
    // replaces unformated values
    $store.charCorrespondences.forEach(pair => {
      const regex = new RegExp(`${pair.corr}`, "g");
      text = text.toLowerCase().replace(regex, pair.char);
    });
    // updates input
    store.updateInput(text);
    // removes all punctuation
    text = text.trim().replace(/[.,\/\?#!$%\^&\*;:{}=\-_`~()]/g, "");
    // calculates new lines positions in textarea to insert <br> in cuneiforms rendering
    const lines = text.split(/[\r\n]/);
    newLinesPos = [];
    if (lines.length > 1) {
      // found new line(s)
      lines.forEach(str => {
        const lastPos = str.split(/ +/g).length;
        newLinesPos =
          newLinesPos.length === 0
            ? [lastPos]
            : [...newLinesPos, lastPos + newLinesPos[newLinesPos.length - 1]];
      });
    }
    const words = text.split(/\s/g);
    // parses syllables
    parsedSyllables = words.map((word, index) => syllableParser(word, index));
    // generates cuneiforms
    cuneiforms = parsedSyllables.map(syllables => ({
      word: syllables.word,
      cuneiforms: cuneiformsGenerator(syllables.syllables)
    }));
    store.updateSyllables(parsedSyllables);
    store.updateCuneiforms(cuneiforms);
    // search words in dictionary
    words.forEach(word => {
      const searchWords = dictionarySearch(word);
      if (searchWords.entry) {
        // prevents double entries
        if (
          !$store.suggestions.filter(
            sugg => sugg.word === searchWords.word && sugg.type === "dictionary"
          ).length &&
          !Object.keys($store.syllabogramsToSwitch).includes(word)
        ) {
          store.addSuggestion({
            ...searchWords,
            text: `<span>${searchWords.word}</span><br/>${
              searchWords.info ? searchWords.info.text + "<br/>" : ""
            }<span class="cuneiform-sign">${
              searchWords.entry.cuneiform.sign
            }</span>`,
            type: "dictionary"
          });
        }
      }
    });
    $store.suggestions.map(sugg => {
      if (sugg.type === "dictionary" && !words.includes(sugg.word)) {
        // cleans up suggestions that are not in the text anymore
        store.removeSuggestion(sugg.word, "dictionary");
      }
      if (Object.keys($store.syllabogramsToSwitch).includes(sugg.word)) {
        // cleans up words that have been added to the syllabogram to switch list
        store.removeSuggestion(sugg.word, "dictionary");
      }
    });
    // creates stress markers for words
    const stressedWords = Object.keys($store.words).map(word =>
      stressMarker(word, $store.words[word])
    );
    store.updateStressedWords(stressedWords);
  };

  const displayStress = word => {
    if (
      word.stressPosition === undefined ||
      word.word.length <= word.stressPosition
    )
      return "---";

    return (
      word.word.slice(0, word.stressPosition) +
      "<strong><u>" +
      word.word[word.stressPosition] +
      "</u></strong>" +
      word.word.slice(word.stressPosition + 1)
    );
  };

  const addLogogram = info => {
    if (info.baseForm) {
      store.addLogogram({ word: info.word, sign: info.entry.cuneiform.sign });
    }
  };

  onMount(() => {
    textareaRef = document.getElementById("textarea-input");
    store.updateInput("");
    store.resetWords();
    store.updateStressedWords([]);
  });
</script>

<style>
  .input {
    width: 100%;
    min-height: 200px;
    margin-bottom: 20px;
  }

  .latin-code {
    padding-left: 5px;
  }

  .cuneiforms {
    min-height: 65px;
    line-height: 2 !important;
    margin-bottom: 20px;
  }

  .suggestions {
    border-top: solid 1px #f5f5f5;
  }

  .options {
    padding: 0px 0px 20px 0px;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
  }

  .information {
    display: flex;
    flex-direction: row;
    justify-content: center;
    padding-bottom: 20px;
  }

  .syllabary {
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding-bottom: 20px;
  }
</style>

<div class="columns" style="padding-top:30px">
  <div class="column is-three-fifths is-offset-one-fifth has-text-centered">
    <textarea
      class="input"
      id="textarea-input"
      on:input={processInput}
      value={$store.input}
      placeholder="Type your text here" />
    <div class="tabs is-toggle is-centered is-small ">
      <ul>
        {#each $store.charCorrespondences as chars}
          <li
            class="has-background-white"
            on:click|preventDefault={() => insertTextAtCursor(textareaRef, chars.char)}>
            <a href="#/">
              {chars.char}
              <span class="latin-code is-size-7">({chars.corr})</span>
            </a>
          </li>
        {/each}
      </ul>
    </div>
    <div class="options is-size-7">
      <label class="checkbox">
        <input
          type="checkbox"
          checked={$store.doubleLongVowels}
          on:change={() => {
            store.updateLongVowelDisplay();
            document
              .getElementById('textarea-input')
              .dispatchEvent(new Event('input', { bubbles: true }));
          }} />
        Duplicate long vowels
      </label>
      <label class="checkbox">
        <input
          type="checkbox"
          checked={$store.interpunct}
          on:change={() => {
            store.updateInterpunct();
          }} />
        Middle point
      </label>
    </div>
    <div class="box">
      <div class="tabs is-centered is-small">
        <ul>
          <li
            class:is-active={selectedTab === 'detailed'}
            on:click|preventDefault={() => (selectedTab = 'detailed')}>
            <a href="#/">Detailed</a>
          </li>
          <li
            class:is-active={selectedTab === 'raw'}
            on:click|preventDefault={() => (selectedTab = 'raw')}>
            <a href="#/">Raw</a>
          </li>
        </ul>
      </div>
      <div class="cuneiforms">
        {#if selectedTab === 'detailed'}
          {#each $store.input
            .trim()
            .replace(/[.,\/\?#!$%\^&\*;:{}=\-_`~()]/g, '')
            .split(/\s+/)
            .filter(el => el) as word, index}
            {#if $store.words[word].syllables !== 'ERROR'}
              <CuneiformWord {word} {newLinesPos} wordPos={index + 1} />
              {#if $store.interpunct && index < Object.keys($store.words).length - 1}
                <span class="is-size-5">&#5867;</span>
              {/if}
            {:else}Ø{/if}
          {:else}
            <span>Cuneiform Rendering</span>
          {/each}
        {:else if selectedTab === 'raw'}
          <RawCuneiforms />
        {/if}
      </div>
      <div class="columns is-multiline suggestions">
        {#each $store.suggestions as sugg}
          <div class="column is-2 is-narrow">
            {#if sugg.type === 'dictionary'}
              <span
                class="is-size-7"
                style={sugg.baseForm ? 'cursor:pointer' : 'cursor: not-allowed'}
                on:click={() => addLogogram(sugg)}>
                {@html sugg.text}
              </span>
            {:else}
              <span class="is-size-7">
                {@html sugg.text}
              </span>
            {/if}
          </div>
        {:else}
          <div class="column is-12 has-text-centered is-size-7">
            No suggestion
          </div>
        {/each}
      </div>
    </div>
    <div class="box">
      <h3 class="subtitle">Stress:</h3>
      <div>
        {#each $store.stressedWords as word, index}
          <span>
            {@html displayStress(word).trim()}
          </span>
          {#if index < $store.stressedWords.length - 1}-&nbsp;{/if}
        {:else}No input{/each}
      </div>
    </div>
    <div class="box">
      <h3 class="subtitle">Information:</h3>
      <div class="columns is-multiline">
        {#each Object.keys($store.words) as word}
          <div class="column is-half">
            <table
              class="table is-bordered is-striped is-narrow is-hoverable
              is-fullwidth">
              <tbody>
                <tr>
                  <td>Word</td>
                  <td>{word}</td>
                </tr>
                <tr>
                  <td>Syllable Count</td>
                  <td>{$store.words[word].syllableCount}</td>
                </tr>
                <tr>
                  <td>Syllables</td>
                  <td>
                    {$store.words[word].syllables === 'ERROR' ? 'ERROR' : $store.words[word].syllables.join('/')}
                  </td>
                </tr>
                <tr>
                  <td>Cuneiform Breakdown</td>
                  <td>
                    {$store.words[word].syllables === 'ERROR' ? 'ERROR' : $store.words[
                          word
                        ].cuneiforms
                          .map(symbol => symbol.cuneiformBreakDown.join('-'))
                          .join('-')}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        {:else}
          <div class="column is-fullwidth has-text-centered">No info</div>
        {/each}
      </div>
    </div>
    <div class="box syllabary">
      <h3 class="subtitle">Syllabary</h3>
      <div class="table-container">
        <table class="table is-narrow is-striped is-fullwidth">
          <thead>
            <tr>
              <td />
              <td>-a</td>
              <td>-e</td>
              <td>-i</td>
              <td>-u</td>
              <td>a-</td>
              <td>e-</td>
              <td>i-</td>
              <td>u-</td>
            </tr>
          </thead>
          <tbody>
            {@html $store.consonants
              .map((cons, i) => {
                let vowels = '';
                if (i === 0) {
                  vowels = `<tr>
                  <td style="vertical-align:middle" />
                  <td class="is-size-4-desktop is-size-5-touch cuneiform-sign">${syllabary['a'].sign}</td>
                  <td class="is-size-4-desktop is-size-5-touch cuneiform-sign">${syllabary['e'].sign}</td>
                  <td class="is-size-4-desktop is-size-5-touch cuneiform-sign">${syllabary['i'].sign}</td>
                  <td class="is-size-4-desktop is-size-5-touch cuneiform-sign">${syllabary['u'].sign}</td>
                  <td />
                  <td />
                  <td />
                  <td />
                </tr>`;
                }
                return `${vowels}<tr><td style="vertical-align:middle">${cons}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary[cons + 'a'].unique ? 'has-text-dark' : ''}" title="${cons + 'a'}">${syllabary[cons + 'a'].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary[cons + 'e'].unique ? 'has-text-dark' : ''}" title="${cons + 'e'}">${syllabary[cons + 'e'].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary[cons + 'i'].unique ? 'has-text-dark' : ''}" title="${cons + 'i'}">${syllabary[cons + 'i'].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary[cons + 'u'].unique ? 'has-text-dark' : ''}" title="${cons + 'u'}">${syllabary[cons + 'u'].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary['a' + cons].unique ? 'has-text-dark' : ''}" title="${'a' + cons}">${syllabary['a' + cons].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary['e' + cons].unique ? 'has-text-dark' : ''}" title="${'e' + cons}">${syllabary['e' + cons].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary['i' + cons].unique ? 'has-text-dark' : ''}" title="${'i' + cons}">${syllabary['i' + cons].sign || 'Ø'}</td>
                        <td class="is-size-4-desktop is-size-5-touch cuneiform-sign ${!syllabary['u' + cons].unique ? 'has-text-dark' : ''}" title="${'u' + cons}">${syllabary['u' + cons].sign || 'Ø'}</td>
                    </tr>`;
              })
              .join('')}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>
<footer>
  <div class="information">
    <div class="tags has-addons">
      <span class="tag is-dark">Logograms</span>
      <span class="tag is-info">
        {Object.keys(dictionary)
          .map(letter => Object.keys(dictionary[letter]).length)
          .reduce((a, b) => a + b)}
      </span>
    </div>
  </div>
</footer>

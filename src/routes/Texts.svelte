<script>
  import texts from "../databases/texts.json";
</script>

<main>
  <h1 class="title has-text-centered">Texts in Akkadian</h1>
  {#each texts as text}
    <div class="tile is-ancestor">
      <div class="tile is-vertical is-12">
        <div class="tile is-parent">
          <article class="tile is-child box subtitle">{text.title}</article>
        </div>
        <div class="tile">
          <div class="tile is-parent is-vertical">
            <article class="tile is-child box">
              {@html text.akkadian}
            </article>
            <article class="tile is-child box">
              {@html text.english}
            </article>
          </div>
          <div class="tile is-parent">
            <article
              class="tile is-child box cuneiform-sign is-size-5"
              style="position:relative">
              <div
                style="position:absolute;top:50%;transform:translateY(-50%);">
                {@html text.cuneiforms}
              </div>
            </article>
          </div>
        </div>
      </div>
    </div>
  {/each}
</main>

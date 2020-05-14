<script>
  import { fly, fade } from "svelte/transition";

  let openSidebar = false;
</script>

<style>
  nav {
    -webkit-box-shadow: -1px 3px 22px 0px rgba(203, 213, 224, 1);
    -moz-box-shadow: -1px 3px 22px 0px rgba(203, 213, 224, 1);
    box-shadow: -1px 3px 22px 0px rgba(203, 213, 224, 1);
  }

  .title-logo {
    text-align: center;
  }

  .mobile-sidebar {
    position: fixed;
    top: 0px;
    left: 0px;
    height: 100vh;
    z-index: 100;
    width: 100%;
    margin: 0px;
  }

  .mobile-sidebar__nav {
    background-color: white;
  }

  .mobile-sidebar__cover {
    position: fixed;
    top: 0px;
    left: 0px;
    background-color: black;
    opacity: 0.4;
    z-index: 99;
    height: 100vh;
    width: 100%;
  }

  .close-sidebar {
    width: 15%;
    height: 10%;
    background-color: transparent;
    position: fixed;
    top: 0;
    right: 0;
  }

  @media only screen and (min-width: 1024px) {
    .logo-img {
      max-height: 3rem;
      padding-right: 20px;
    }
  }
</style>

<nav
  class="navbar is-warning is-spaced is-fixed-top"
  role="navigation"
  aria-label="main navigation">
  <div class="navbar-brand">
    <a class="navbar-item" href="#/">
      <img src="logo.png" class="logo-img" alt="logo" />
      <span class="title is-4 title-logo is-hidden-mobile">
        Akkadian
        <br />
        Revival
      </span>
    </a>

    <a
      href="#/"
      role="button"
      class="navbar-burger burger"
      class:is-active={openSidebar}
      aria-label="menu"
      aria-expanded="false"
      data-target="navbarMenu"
      on:click|preventDefault={() => {
        openSidebar = !openSidebar;
      }}>
      <span aria-hidden="true" />
      <span aria-hidden="true" />
      <span aria-hidden="true" />
    </a>
  </div>

  <div id="navbarMenu" class="navbar-menu">
    <div class="navbar-end">
      <a class="navbar-item" href="#/conjugator">Conjugator</a>
      <a class="navbar-item" href="#/keyboard">Keyboard</a>
      <div class="navbar-item has-dropdown is-hoverable">
        <a class="navbar-link" href="#/" on:click={e => e.preventDefault()}>
          More
        </a>

        <div class="navbar-dropdown">
          <a class="navbar-item" href="#/texts">Texts</a>
          <hr class="navbar-divider" />
          <a class="navbar-item">Contact</a>
          <a
            class="navbar-item"
            href="https://github.com/claudebarde/akkadian-revival"
            target="_blank"
            rel="noopener noreferrer">
            Github Repo
          </a>
          <hr class="navbar-divider" />
          <a class="navbar-item">Report an issue</a>
        </div>
      </div>
      <a class="navbar-item" href="#/participate">Participate</a>
    </div>
  </div>
</nav>

<!-- SIDEBAR FOR MOBILE VIEW ONLY -->
{#if openSidebar}
  <div class="mobile-sidebar__cover" transition:fade={{ duration: 300 }} />
  <div class="mobile-sidebar columns is-mobile is-hidden-desktop">
    <div
      class="column is-one-third mobile-sidebar__nav"
      transition:fly={{ x: -100, duration: 300 }}>
      <div class="menu">
        <p class="menu-label">General</p>
        <ul class="menu-list">
          <li>
            <a href="#/" on:click={() => (openSidebar = false)}>Home</a>
          </li>
          <li>
            <a href="#/conjugator" on:click={() => (openSidebar = false)}>
              Conjugator
            </a>
          </li>
          <li>
            <a href="#/keyboard" on:click={() => (openSidebar = false)}>
              Keyboard
            </a>
          </li>
          <li>
            <a href="#/participate" on:click={() => (openSidebar = false)}>
              Participate
            </a>
          </li>
        </ul>
        <p class="menu-label">Resources</p>
        <ul class="menu-list">
          <li>
            <a href="#/texts" on:click={() => (openSidebar = false)}>Texts</a>
          </li>
        </ul>
      </div>
    </div>
    <div class="column is-two-thirds">
      <div
        class="close-sidebar"
        on:click={() => {
          openSidebar = false;
        }} />
    </div>
  </div>
{/if}

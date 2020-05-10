<style>
  nav {
    -webkit-box-shadow: -1px 3px 22px 0px rgba(203, 213, 224, 1);
    -moz-box-shadow: -1px 3px 22px 0px rgba(203, 213, 224, 1);
    box-shadow: -1px 3px 22px 0px rgba(203, 213, 224, 1);
  }

  .title-logo {
    text-align: center;
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
      <img src="logo.png" class="logo-img" />
      <span class="title is-4 title-logo is-hidden-mobile">
        Akkadian
        <br />
        Revival
      </span>
    </a>

    <a
      role="button"
      class="navbar-burger burger"
      aria-label="menu"
      aria-expanded="false"
      data-target="navbarMenu">
      <span aria-hidden="true" />
      <span aria-hidden="true" />
      <span aria-hidden="true" />
    </a>
  </div>

  <div id="navbarMenu" class="navbar-menu">
    <div class="navbar-end">
      <a class="navbar-item" href="#/conjugator">Conjugator</a>
      <a class="navbar-item" href="#/keyboard">Keyboard</a>
      <a class="navbar-item" href="#/participate">Participate</a>
    </div>
  </div>
</nav>

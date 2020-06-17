<template>
  <nav
    class="navbar container is-spaced bg-dark"
    role="navigation"
    aria-label="main navigation">
    <div class="navbar-brand">
      <button
        class="navbar-burger burger"
        aria-label="Menu"
        :class="{'is-active': isMenuActive}"
        @click="isMenuActive = !isMenuActive"
        @keyup="isMenuActive = !isMenuActive">
        <span aria-hidden="true" />
        <span aria-hidden="true" />
        <span aria-hidden="true" />
      </button>
    </div>
    <div
      class="navbar-menu"
      :class="{'is-active': isMenuActive}">
      <div class="navbar-start">
        <g-link
          to="/collections"
          class="navbar-item">
          Collections
        </g-link>
        <g-link
          to="/"
          class="navbar-item">
          Contact
        </g-link>
        <g-link
          to="/"
          class="navbar-item">
          About
        </g-link>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  data: () => ({
    isMenuActive: false,
    searchTerm: ''
  }),
  computed: {
    cart () { return this.$store.state.cart },
    searchResults () {
      const searchTerm = this.searchTerm
      if (searchTerm.length < 3) return []
      return this.$search.search({ query: searchTerm, limit: 5, suggest: true })
    }
  },
  watch: {
    $route (to, from) {
      this.searchTerm = ''
    }
  }
}
</script>

<style scoped>
.navbar-burger {
  background-color: transparent;
  border: none;
}
.logo {
  font-family: "Prata", BlinkMacSystemFont, -apple-system, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", "Helvetica", "Arial", sans-serif !important;
  text-transform: none;
}
.button {
  font-weight: $weight-semibold;
  text-transform: uppercase;
  font-size: $size-small !important;
  height: 50px !important;
}
.navbar {
  text-transform: uppercase;
  font-size: 0.8rem;
}
</style>

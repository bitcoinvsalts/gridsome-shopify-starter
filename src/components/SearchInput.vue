<template>
  <div class="relative">

    <div class="relative w-40">
      <input
        type="text"
        placeholder="search"
        class="bg-background-form border border-gray-500 rounded-full px-4 pl-10 py-2 outline-none focus:border-green-500 w-40"
        id="search"
        v-model="searchTerm"
      >
      <div class="absolute top-0 ml-3" style="top:10px">
        <svg fill="currentColor" class="text-gray-500 h-5 w-5" viewBox="0 0 24 24" width="24" height="24"><path class="heroicon-ui" d="M16.32 14.9l5.39 5.4a1 1 0 0 1-1.42 1.4l-5.38-5.38a8 8 0 1 1 1.41-1.41zM10 16a6 6 0 1 0 0-12 6 6 0 0 0 0 12z"></path></svg>
      </div>
      <div
        v-if="searchTerm.length > 0"
        class="absolute top-0 right-0 text-2xl mr-3 cursor-pointer text-gray-600 hover:text-gray-800"
        style="top:2px;"
        @click="reset"
      >
        &times;
      </div>
    </div>

    <transition name="fade">
      <div v-if="searchTerm.length > 0" class="normal-case absolute border left-0 right-0 w-108 text-left mb-4 mt-2 rounded-lg shadow overflow-hidden z-10 overflow-y-auto" style="max-height: 32rem">
        <div class="flex flex-col" ref="results">
          <div class="navbar-dropdown">
            <g-link
              v-for="({ node: result, path }) in searchResults"
              :key="result.id"
              :to="path"
              class="navbar-item">
              {{ result.title }}
            </g-link>
          </div>
        </div>
      </div>
    </transition>
    
  </div>
</template>

<script>
import SearchFocus from './SearchFocus'

export default {
  components: {
    SearchFocus,
  },
  created() {
  },
  data() {
    return {
      searchTerm: '',
    }
  },
  computed: {
    searchResults () {
      const searchTerm = this.searchTerm
      if (searchTerm.length < 3) return []
      return this.$search.search({ query: searchTerm, limit: 5, suggest: true })
    },
  },
  watch: {
    $route (to, from) {
      this.searchTerm = ''
    }
  },
  methods: {
    reset() {
      this.searchTerm = ''
    },
  }
}
</script>

<style scoped>
  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>


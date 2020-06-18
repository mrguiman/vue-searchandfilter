<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <SearchAndFilter :filters="searchFilters" />
  </div>
</template>

<script>
import SearchAndFilter from './components/SearchAndFilter.vue'

export default {
  name: 'App',
  components: {
    SearchAndFilter
  },
  data() {
    return {
      searchFilters: ["country"]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

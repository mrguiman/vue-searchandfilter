<template>
  <div class="searchandfilter">
    <div class="input-wrapper">
      <div class="active-filters" v-if="activeFilters.length > 0">
        <div v-for="filter in activeFilters" v-bind:key="filter.filter" class="active-filter">
          <span>{{ filter.filter }}:{{filter.value}}</span>
        </div>
      </div>
      <input type="text" v-model="internalValue" ref="rawInput" />
    </div>

    <div v-if="availableFilters.length > 0" class="filters-dropdown">
      <div v-for="filter in availableFilters" v-bind:key="filter" class="filter-line" v-on:click="addFilter(filter)">
        <span>{{ filter }}:</span> <span>{{ internalValue }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SearchAndFilter',
  props: {
    filters: Array
  },

  data() {
    return {
      internalValue: "coucou",
      activeFilters: []
    }
  },

  computed: {
    unboundKeywords() {
      return this.internalValue
    },
    availableFilters() {
      return this.filters.filter(val => {
        return this.activeFilters.map(x => x.filter).indexOf(val) === -1
      }) 
    }
  },

  methods: {
    addFilter(filter) {
      this.activeFilters.push({
        filter: filter,
        value: this.internalValue
      });
      this.internalValue = ""
      this.$refs.rawInput.focus();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.searchandfilter {
  width: 500px;
  margin: auto;
}
.input-wrapper {
  display: flex;
  align-items: center;
  width: 100%;
  height: 40px;
  border: 1px solid #e2e2e2;
  border-radius: 5px;
  box-sizing: border-box;
  padding: 0 8px;
}
.input-wrapper .active-filters {
  display: inline-block;
}
.active-filter {
  background-color: #e2e2e2;
  border-radius: 8px;
  padding: 4px;
}
.input-wrapper > input {
  display: inline-block;
  background: none;
  border: none;
  height: 100%;
  padding: 0 8px;
  flex: 1;
}

.filters-dropdown {
  margin-top: 8px;
  padding: 8px 0;
  border: 1px solid #e2e2e2;
  text-align: left;
  border-radius: 5px;
}

.filter-line {
  padding: 8px 16px;
  cursor: pointer;
  line-height: 16px;
}

.filter-line:before {
  display: inline-block;
  content: "i";
  width: 16px;
  height: 16px;
  margin-right: 8px;
  color: white;
  background-color: black;
  text-align: center;
  font-size: 10px;
  border-radius: 8px;
}

.filter-line:hover {
  background-color: #e2e2e2;
}
</style>

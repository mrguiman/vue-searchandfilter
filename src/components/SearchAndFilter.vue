<!-- Input is in a working state but scrolling can be a pain because the component is composed of
divs and a raw input -->
<template>
  <div class="searchandfilter">
    <div class="input-wrapper">
      <div class="active-filters" v-if="activeFilters.length > 0">
        <div v-for="filter in activeFilters" v-bind:key="filter.filter" class="active-filter">
          <span>{{ filter.filter }}:{{filter.value}}</span>
        </div>
      </div>
      <input type="text" v-model="searchKeywords" ref="rawInput" v-on:keyup="handleInputs" />
    </div>

    <div v-if="displayedFilters.length > 0" class="filters-dropdown">
      <div v-for="filter in displayedFilters" v-bind:key="filter" class="filter-line" v-on:click="handleFilterClick(filter)">
        <span>{{ filter }}:</span><span>{{ searchKeywords }}</span>
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
      searchKeywords: "",
      activeFilters: []
    }
  },

  computed: {
    availableFilters() {
      return this.filters.filter(val => {
        return this.activeFilters.map(x => x.filter).indexOf(val) === -1
      })
    },
    displayedFilters() {
      return this.availableFilters.filter(val => {
        return this.searchKeywords.indexOf(`${val}:`) === -1
      });
    },
    searchFilterRegex() {
      let availableFiltersRegexSubstring = this.availableFilters.reduce((acc, curr) => {
        if (acc.length > 0) {
          acc += "|"
        }
        acc += `\\b${curr}\\b`
        return acc;
      }, '');

      return `(${availableFiltersRegexSubstring}):([^\\s"']+ |"[^"]*"|'[^']*' )`
    },
  },

  methods: {
    handleFilterClick(filter) {
      if (this.searchKeywords.length > 0) {
        this.addFilter(filter, this.searchKeywords)
      } else {
        this.searchKeywords += `${filter}:`
        this.$refs.rawInput.focus();
      }
    },
    addFilter(filter, value) {
      let filterValue = value || this.searchKeywords;

      this.activeFilters.push({
        filter: filter,
        value: filterValue
      });

      // Clean up the input after adding the filter
      this.searchKeywords = this.searchKeywords.replace(filterValue, '').trim();
      this.searchKeywords = this.searchKeywords.replace(`${filter}:`, '').trim();

      this.$refs.rawInput.focus();
    },
    popFilter() {
          // Pressed backspace on empty input means we get rid of the last filter
          let lastFilter = this.activeFilters.pop();
          if (lastFilter) {
            this.searchKeywords = `${lastFilter.filter}:${lastFilter.value}`;
          }
    },
    tryMatchFilter() {
        const filterMatch = this.searchKeywords.match(new RegExp(this.searchFilterRegex, 'g'));

        if (filterMatch && filterMatch.length > 0) {
          const [filter, value] = filterMatch[0].trim().split(':');
          
          this.addFilter(filter, value);
        }
    },
    handleInputs (e) {
      if (this.searchKeywords.length === 0) {
        if (e.key.toLowerCase() === "backspace") {
          this.popFilter();
        }
      } else if (this.availableFilters.length > 0) {
        this.tryMatchFilter();
      }
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
  overflow: auto;
  -ms-overflow-style: none;
  scrollbar-width: none;
}

.input-wrapper > * {
  flex: 0 0 auto;
}

.input-wrapper::-webkit-scrollbar {
  display: none
}

.input-wrapper .active-filters {
  display: inline-block;
}
.active-filter {
  display:inline-block;
  background-color: #e2e2e2;
  border-radius: 8px;
  padding: 4px;
}
.active-filter+.active-filter {
  margin-left: 8px;
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

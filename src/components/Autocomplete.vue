<template lang="html">
  <div class="autocomplete">
    <input type="text" @input="onChange" v-model="search" @keyup.down="onArrowDown" @keyup.up="onArrowUp" @keyup.enter="onEnter" />
    <ul id="autocomplete-results" v-show="isOpen" class="autocomplete-results">
      <li class="loading" v-if="isLoading">
        Loading results...
      </li>
      <li v-else v-for="(result, i) in results" :key="i" @click="setResult(result)" class="autocomplete-result" :class="{ 'is-active': i === arrowCounter }">
        {{ result.title }}
      </li>
    </ul>
  </div>

</template>

<script>
export default {
  name: "autocomplete",
  props: {
    items: {
      type: Array,
      required: false,
      default: () => []
    },
    isAsync: {
      type: Boolean,
      required: false,
      default: true
    }
  },

  data() {
    return {
      isOpen: false,
      results: [],
      search: "",
      isLoading: false,
      arrowCounter: 0
    };
  },

  methods: {
    onChange() {

      // Let's warn the parent that a change was made
      this.$emit("input", this.search);

      if (this.search.length == 0) {
        this.isOpen = false;
        return false;
      }
      // Is the data given by an outside ajax request?
      else if (this.isAsync) {
        this.isLoading = true;
      } else {
        // Let's search our flat array
        this.filterResults();
        this.isOpen = true;
      }


    },

    filterResults() {
      // first uncapitalize all the things
      this.results = this.items.filter(item => {
        return item.title.toLowerCase().indexOf(this.search.toLowerCase()) == 0;
      });
    },
    setResult(result) {
      this.search = result.name;
      this.isOpen = false;
    },
    onArrowDown() {
      if (this.arrowCounter < this.results.length) {
        this.arrowCounter = this.arrowCounter + 1;
      }
    },
    onArrowUp() {
      if (this.arrowCounter > 0) {
        this.arrowCounter = this.arrowCounter - 1;
      }
    },
    onEnter() {
      this.search = this.results[this.arrowCounter].name;
      this.isOpen = false;
      this.arrowCounter = -1;
    },
    handleClickOutside(evt) {
      if (!this.$el.contains(evt.target)) {
        this.isOpen = false;
        this.arrowCounter = -1;
      }
    }
  },
  watch: {
    items: function(value, oldValue) {
      // actually compare them
      if (this.isAsync) {
        this.results = value;
        this.isOpen = true;
        this.isLoading = false;
      }
    }
  },
  mounted() {
    document.addEventListener("click", this.handleClickOutside);
  },
  destroyed() {
    document.removeEventListener("click", this.handleClickOutside);
  }
}
</script>

<style lang="scss" scoped>
.autocomplete {
    position: relative;
    max-width: 330px;
    margin: auto;
}

.autocomplete-results {
    padding: 0;
    margin: 0;
    border: 1px solid #eeeeee;
    overflow: auto;
    width: 100%;
}

.autocomplete-result {
    list-style: none;
    text-align: left;
    padding: 4px 2px;
    cursor: pointer;
}

.autocomplete-result.is-active,
.autocomplete-result:hover {
    background-color: $color;
    color: white;
}
</style>

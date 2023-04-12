<template>
  <div class="card-search quiz-screen__section flow" :class="{'card-search--sm-visible': showCardSearch}">
      <form class="card-search__form" @submit.prevent="searchCards">
        <input aria-label="card name" type="text" v-model="nameString" autocomplete="off" />
        <button class="button button--primary">Search</button>
      </form>

      <ul class="search-results box box--light" ref="results">
        <li v-for="result in searchResults" :key="result.name" class="search-results__item">
          <a href="#" @click.prevent="selectCard(result)">{{result.name}}</a>
        </li>
      </ul>

      <button @click="closeMenu" class="button--primary card-search__close">Close</button>
    </div>
</template>

<script>
import debounce from "../util/debounce.js";

export default {
  name: "CardSearch",
  props: {
    showCardSearch: Boolean
  },
  data() {
    return {
      apiUrl: "https://db.ygoprodeck.com/api/v7/cardinfo.php?fname=",
      nameString : "",
      searchResults: []
    }
  },
  methods: {
    searchCards: debounce(function () {
      if (this.nameString.length === 0) return;
      
      fetch(this.apiUrl + this.nameString)
        .then(result => result.json())
        .then(result => {
          this.searchResults = result.data;
          this.nameString = "";
          this.$refs.results.scrollTop = 0;
        })
        
    }, 1000, true),
    selectCard(card) {
      const name = card.name;
      this.$emit("card-selected", name);
      
      if (this.showCardSearch) this.$emit("close-menu");
    },
    closeMenu() {
      this.$emit("close-menu");
    }
  }
}
</script>

<style scoped>

.card-search {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex-grow: 1;
}

.card-search__form {
  display: flex;
  align-items: center;
  align-self: stretch;
}

.card-search__form > * + * {
  margin-left: 0.25rem;
}

.card-search input[type="text"] {
  border: 2px solid var(--border-color-medium);
  background-color: var(--box-bg-medium);
  flex-basis: 100%;
}

.card-search__close {
  display: none;
}


@media screen and (max-width: 60em) {
  .card-search {
    display: none;
    margin: 0 auto;
    background-color: var(--box-bg-light);
    border: 3px solid var(--border-color-light);
  }
  
  .card-search--sm-visible {
    display: block;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 10;
  }
  
  .card-search__form button {
    padding: var(--spacing-small-1) var(--spacing-small-2);
    font-size: var(--font-size-1);
  }
  
  .card-search__close {
    display: block;
    width: 100%;
    max-width: 10rem;
    margin-right: auto;
    margin-left: auto;
  }
}

.search-results {
  height: 325px;
  width: 100%;
  overflow: auto;
  
  box-shadow: none;
}

.search-results__item a {
  width: 100%;
  display: inline-block;
  padding: 0.5rem 0.25rem;
  color: var(--text-color-dark);
  font-weight: 800;
  cursor: pointer;
}

.search-results__item > a:hover,
.search-results__item > a:focus,
.search-results__item > a:active {
  background-color: var(--highlight-color);
}

@media screen and (max-width: 35em) {
  .search-results__item a {
    padding: 1rem 0.25rem;
  }
}

</style>
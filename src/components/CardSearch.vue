<template>
  <div>
    <form class="text-form" @submit.prevent="search()">
        <input type="text" name="name" v-model="searchString" autocomplete="off" 
               aria-label="search card name" placeholder="search for cards..." 
               autofocus />
        <button type="submit" class="button button--primary">Search</button>
    </form>

    <ul class="search-results">
        <li v-for="result in searchResults" :key="result.id"
            class="search-result">
          <button @click="selectCardName(result.name)">{{result.name}}</button>
        </li>
    </ul>
  </div>
</template>

<script>
import debounce from "../util/debounce.js";

export default {
  name: "CardSearch",
  data() {
    return {
      searchString: "",
      searchURL: "https://db.ygoprodeck.com/api/v7/cardinfo.php?&fname=",
      searchResults: []
    }
  },
  methods: {
    selectCardName(name) {
      this.$emit("select-card", name);
    },
    search: debounce(function() {
      if (this.searchString.length === 0) return;
      fetch(this.searchURL + this.searchString)
      .then(result => result.json())
      .then(result => {
        this.searchResults = result.data;
        this.searchString = "";
      })
    }, 1000, true)
  }
}
</script>

<style scoped>

  input[type="text"] {
    padding: 0.5em;
    border: none;
  }

  .text-form {
    display: flex;
    flex-direction: row;
    margin-bottom: 0.25em;
  }

  .text-form input[type="text"] {
    margin-right: 0.25em;
  }

  .search-results {
    list-style: none;
    height: 375px;
    width: 450px;
    padding: 0.5em;
    border: 1px solid hsl(200, 40%, 75%);
    background-color: rgba(0, 0, 0, 0.25);
    overflow: auto;
  }

  .search-result {
    margin-bottom: 0.5em;
  }

  .search-result button {
    border: none;
    background: transparent;
    color: #fff;
    font-family: inherit;
    font-size: 1rem;
    cursor: pointer;
  }

  .search-result button:hover,
  .search-result button:focus {
    color: hsl(200, 55%, 85%);
  }

</style>
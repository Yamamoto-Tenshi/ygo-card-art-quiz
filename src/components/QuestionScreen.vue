<template>
  <div class="question-screen screen container">

    <div class="card-image-container" v-if="currentCard">
      <p class="question-number">Card {{currentQuestion}}</p>
      <question-card :card="currentCard"
                     :joker="jokerUsed"
                     :difficulty="difficulty">
      </question-card>
            
      <div>
        <p v-show="!answer.length" class="selected-card">What's this Cards name?</p>
        <transition name="scale">
          <p v-show="answer.length" :key="answer" class="selected-card">{{answer}}</p>
        </transition>
        
        <div class="button-group">
          <button @click="emitAnswer('')" class="button button--primary">Dont Know</button>
                
          <button class="button button--primary"
                  :disabled="jokerUsed || jokersLeft === 0"
                  @click="$emit('joker-used')">
            Use Joker
          </button>
          <button v-show="answer.length" @click="emitAnswer(answer)" class="button button--secondary">Confirm</button>
        </div>
      </div>
            
      <div class="joker-container">
        <span>Jokers left ({{jokersLeft}}): </span>
        <img v-for="(joker, index) in jokersLeft" :key="joker + index"
            class="joker"
            src="../assets/images/goat.png" 
        />
      </div>
    </div>

    <div v-show="!currentCard" class="card-image-placeholder">
      <div class="spinner"></div>
    </div>

    <card-search ref="cardsearch" 
                 @select-card="getCardName"
                 @menu-toggled="toggleMenu">
    </card-search>
  </div>
</template>

<script>

import CardSearch from "./CardSearch.vue";
import QuestionCard from "./QuestionCard.vue";

export default {
  name: "QuestionScreen",
  props: {
    currentCard: Object,
    difficulty: String,
    currentQuestion: Number,
    jokerUsed: Boolean,
    jokersLeft: Number,
  },
  data() {
    return {
      answer: "",
      showMenu: false
    }
  },
  methods: {
    emitAnswer(answer) {
      this.answer = answer;
      this.$emit("answer-emitted", this.answer);
      this.answer = "";
    },
    getCardName(name) {
      this.answer = name;
    },
    toggleMenu() {
      this.$refs.cardsearch.toggleVisibility();
      this.showMenu = !this.showMenu;
      this.$emit("menu-toggled", this.showMenu);
    }
  },
  components: {
    CardSearch,
    QuestionCard
  }
}

</script>

<style scoped>

  .question-screen {
    height: 75%;
    display: flex;
    justify-content: space-between;
    position: relative;
  }

  @media screen and (max-height: 47em) {
    .question-screen {
      height: 95%;
    }
  }

  @media screen and (max-width: 70em) {
    .question-screen {
      justify-content: center;
      align-items: center;
    }
  }

  .question-number {
    position: absolute;
    bottom: 0;
    right: 0.25em;
    font-size: 1.5rem;
    font-weight: 800;
    color: hsl(255, 40%, 70%);
  }

  .card-image-placeholder {
    width: 350px;
    height: 350px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .selected-card {
    margin-bottom: 0.75em;
    font-size: 1.15rem;
    font-weight: 800;
  }

  .scale-enter-active.selected-card {
    animation: scale-in 0.35s ease-in-out;
  }

  .joker-container {
    display: flex;
    align-items: center;
    margin-top: 0.75em;
  }

  .joker {
    width: 30px;
    margin-left: 0.5em;
  }

</style>
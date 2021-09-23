<template>
  <main id="app" class="app-container" v-cloak>
    <header class="header" v-show="showHeader">
      <div class="container">
        <span class="logo">YGO CAQ</span>
        
        <div class="menu"
             v-show="!quizStarted || showFinishScreen">
          <label for="difficulty-level">Select Difficulty</label>
          <select class="level-select" id="difficulty-level"
                  v-model="difficulty">
            <option value="easy">easy</option>
            <option value="medium">medium</option>
            <option value="hard">hard</option>
          </select>
        </div>
        <button v-show="showQuestion" 
                @click="$refs.questionscreen.toggleMenu()"
                class="button button--primary show-card-search"
        >{{searchMenuText}}</button>
      </div>
    </header>
    
    <div class="center">
      <start-screen  v-if="!quizStarted" @start-quiz="startQuiz"></start-screen>

      <question-screen v-show="showQuestion"
                       ref="questionscreen"
                       :currentCard="currentCard"
                       :difficulty="difficulty"
                       :currentQuestion="currentQuestion"
                       :jokerUsed="jokerUsed"
                       :jokersLeft="jokersLeft"
                       @answer-emitted="evaluateAnswer"
                       @joker-used="useJoker"
                       @menu-toggled="updateMenuToggleText">
      </question-screen>

      <current-result-screen v-show="showCurrentQuestionResult"
                             :currentCard="currentCard"
                             :currentQuestionResult="currentQuestionResult"
                             :showCurrentQuestionResult="showCurrentQuestionResult"
                             @next-card="next">
      </current-result-screen>

      <finish-screen v-show="showFinishScreen"
                     :score="score"
                     :currentQuestion="currentQuestion"
                     @play-again="startQuiz">
      </finish-screen>
    </div>
  </main>
</template>

<script>

import StartScreen from "./components/StartScreen.vue";
import QuestionScreen from "./components/QuestionScreen.vue";
import CurrentResultScreen from "./components/CurrentResultScreen.vue";
import FinishScreen from "./components/FinishScreen.vue";

export default {
  name: "card-art-quiz",
  data() {
    return {
      quizStarted: false,
      randomCardURL: "https://db.ygoprodeck.com/api/v7/randomcard.php",
      difficulty: "easy",
      showSearchMenu: false,
      currentQuestion: 1,
      currentCard: null,
      currentQuestionResult: "wrong",
      showCurrentQuestionResult: false,
      showQuestion: false,
      showFinishScreen: false,
      jokerUsed: false,
      jokersLeft: 3,
      score: 0,
    }
  },
  methods: {
    startQuiz() {
      this.quizStarted = true;
      this.jokersLeft = 3;
      this.jokerUsed = false;
      this.score = 0;
      this.currentQuestion = 1;
      this.showFinishScreen = false;
      this.showQuestion = true;
      this.getRandomCard();
      
    },
    getRandomCard() {
      fetch(this.randomCardURL)
      .then(result => result.json())
      .then(result => {
        if (result.type === "Skill" || result.id >= 100000000 || result.type === "Token") {
          setTimeout(() => this.getRandomCard(), 500);
        }
        else {
          this.currentCard = result;
        }
      })
    },
    evaluateAnswer(answer) {
      console.log(answer)
      if (this.currentCard.name.toLowerCase() === answer.toLowerCase()) {
        this.score++;
        this.currentQuestionResult = "right";
      }
      else {
        this.currentQuestionResult = "wrong";
      }
      
      this.showQuestion = false;
      this.showCurrentQuestionResult = true;
    },
    next() {
      this.currentCard = null;
      
      if (this.currentQuestion === 10) {
        this.showCurrentQuestionResult = false;
        this.showFinishScreen = true;
      }
      else {
        this.currentQuestion++;
        this.jokerUsed = false;
        this.showQuestion = true;
        this.showCurrentQuestionResult = false;
        this.getRandomCard();
      }
    },
    useJoker() {
      if (this.jokersLeft > 0) {
        this.jokersLeft--;
        this.jokerUsed = true;
      }
    },
    updateMenuToggleText(showMenu) {
      this.showSearchMenu = showMenu;
    }
  },
  computed: {
    showHeader() {
      return this.quizStarted === false || 
             this.showCurrentQuestionResult === false && this.showFinishScreen === true ||
             this.showCurrentQuestionResult === false && this.showQuestion === true
    },
    searchMenuText() {
      return this.showSearchMenu ? "Close Search Menu" : "Open Search Menu"
    }
  },
  components: {
    StartScreen,
    QuestionScreen,
    CurrentResultScreen,
    FinishScreen
  }
}
</script>

<style>

  *,
  *::after,
  *::before {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  body {
    line-height: 1.6;
    font-family: 'Roboto', sans-serif;
  }

  h1,
  h2,
  h3 {
    line-height: 1.2;
  }

  img {
    max-width: 100%;
    display: block;
  }

  .container {
    width: 80%;
    max-width: 1025px;
    margin: 0 auto;
  }

  @media screen and (max-width: 70em) {
    .container {
      width: 95%;
    }
  }

  .button {
    border: none;
    padding: 0.55em 1.5em;
    font-size: inherit;
    cursor: pointer;
  }

  button + button {
    margin-left: 0.25em;
  }

  .button:disabled {
    cursor: not-allowed;
    opacity: 0.75;
    background: #999;
  }

  .button--primary {
    background-color: hsl(255, 40%, 65%);
    background-image: linear-gradient(hsl(255, 40%, 65%), hsl(255, 40%, 50%));
    color: #fff;
  }

  .button--primary:not(:disabled):hover,
  .button--primary:not(:disabled):focus {
    background-color: hsl(255, 45%, 55%);
    background-image: linear-gradient(hsl(255, 40%, 55%), hsl(255, 40%, 35%));
  }

  .button--secondary {
    background-color: hsl(160, 40%, 40%);
    background-image: linear-gradient(hsl(165, 40%, 45%), hsl(160, 40%, 30%));
    color: #fff;
  }

  .button--secondary:not(:disabled):hover,
  .button--secondary:not(:disabled):focus {
    background-color: hsl(150, 40%, 30%);
    background-image: linear-gradient(hsl(165, 40%, 35%), hsl(160, 40%, 20%));
  }

  .button--big {
    font-size: 1.55rem;
  }

  .button-group {
    display: flex;
  }

  .button-group > button + button {
    margin-left: 0.25em;
  }

  /* animations */

  .scale-enter-active {
    transform: scale(0);
    opacity: 0.5;
    transform-origin: center;
  }

  @keyframes scale-in {
    0% {
      transform: scale(0);
      opacity: 0.5;
    }
    100% {
      transform: scale(1);
      opacity: 1;
    }
  }

  .slide-enter-active {
    transform: translateX(-100%);
    animation: slide-in 0.25s ease-out;
  }

  @keyframes slide-in {
    0% {
      transform: translateX(-100%);
    }
    
    100% {
      transform: translateX(0);
    }
  }

  .app-container {
    width: 100%;
    height: 100vh;
    overflow: hidden;
    background-color: hsl(220, 15%, 55%);
    background-image: url("./assets/images/bg.jpg");
    background-size: cover;
    background-position:center;
    background-repeat: no-repeat;
    background-blend-mode: hard-light;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #fff;
  }

  .center {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
  }

  @media screen and (max-height: 47em) {
    .app-container {
      justify-content: flex-start;
    }
    
    .center {
      height: 100%;
      justify-content: center;
    }
  }

  .spinner {
    width: 100px;
    height: 100px;
    border: 7px solid #fff;
    border-radius: 50%;
    border-top-color: hsl(200, 50%, 50%);
    animation: spin 1s forwards linear infinite;
  }

  @keyframes spin {
    0% {transform: rotate(0)}
    100% {transform: rotate(360deg)}
  }

  /* header */

  .header {
    padding: 1em 0;
    background-color: rgba(0, 0, 0, 0.5);
    width: 100%;
  }

  .header > .container {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .level-select {
    margin-left: 0.5em;
    padding: 0.25em;
    background-color: black;
    color: #fff;
  }

  .logo {
    font-weight: 800;
  }

  .show-card-search {
    display: none;
  }

  @media screen and (max-width: 70em) {
    .show-card-search {
      display: inline-block;
    }
  }

  .screen {
    padding: 3em;
    background-color: rgba(0, 0, 10, 0.35);
    box-shadow: 0 0 8px rgba(0, 0, 10, 0.25),
                0 0 8px rgba(0, 0, 10, 0.25);
  }

  @media screen and (max-height: 47em) {
    .screen {
      height: 95%;
    }
  }

  @media screen and (max-width: 70em) {
    .screen {
      padding: 0.5em;
    }
  }

  [v-cloak] { display: none; }

</style>

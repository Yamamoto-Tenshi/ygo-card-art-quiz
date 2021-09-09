<template>
  <main id="app" class="app-container" v-cloak>
    <header class="header" v-show="showHeader">
      <div class="container">
        <span class="logo" v-show="showHeader">YGO CAQ</span>
        
        <div class="menu"
            v-show="!quizStarted || showFinishScreen">
          <label for="difficulty-level">Select Difficulty</label>
          <select class="level-select" id="difficulty-level"
                  v-model="difficulty">
            <option disabled value="">select difficulty</option>
            <option value="easy">easy</option>
            <option value="medium">medium</option>
            <option value="hard">hard</option>
          </select>
        </div>
      </div>
    </header>
    
    <div class="center">
      <div class="start-screen screen container" v-if="!quizStarted">
        <div>
          <h1 class="main-title" >YGO Card Art Quiz</h1>
          <p class="intro">
            In this game you have to guess which Yu-Gi-Oh! card you see on the screen based
            on the artwork. However, only a small part of the image is shown!
            Each round consists of 10 cards. how many can you guess?
          </p>
          <button class="button button--primary button--big"
                  @click="startQuiz()">
            Play
          </button>
        </div>
        <img src="./assets/images/start-image.jpg" />
      </div>

      <div class="question-screen screen container"
          v-show="showQuestion">

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
            
            <div>
              <button @click="evaluateAnswer" class="button button--primary">dont know</button>
              
              <button class="button button--primary"
                      :disabled="jokerUsed || jokersLeft === 0"
                      @click="useJoker">
                Use Joker
              </button>
              <button v-show="answer.length" @click="evaluateAnswer" class="button button--secondary">confirm</button>
            </div>
          </div>
          
          <div class="joker-container">
            <span>Jokers left ({{jokersLeft}}): </span>
            <img v-for="(joker, index) in jokersLeft" :key="joker + index"
                class="joker"
                src="./assets/images/goat.png" 
            />
          </div>
        </div>

        <div v-show="!currentCard" class="card-image-placeholder">
          <div class="spinner"></div>
        </div>

        <card-search @select-card="getCardName"></card-search>
      </div>

      <div class="current-result-screen container"
          v-show="showCurrentQuestionResult">

        <transition name="slide">
          <img :src="getCardImage" 
              :title="currentCard.name"
              class="card"
              v-if="currentCard"
              v-show="showCurrentQuestionResult"/>
        </transition>

        <div class="current-result-container">
          <transition name="scale">
            <img :src="currentQuestionResult === 'wrong' ? getImage('/wrong.png') : getImage('/right.png') "
                 alt="result"
                class="current-result"
                v-show="showCurrentQuestionResult" />
          </transition>
          <button @click="next()"
                  class="button button--primary button--big">Next</button>
        </div>
      </div>

      <div class="finish-screen container screen"
          v-show="showFinishScreen">
        <p class="score">Score: {{score}} out of {{currentQuestion}}</p>
        <button class="button button--primary button--big" @click="startQuiz">Play Again</button>
      </div>
    </div>
  </main>
</template>

<script>
import CardSearch from "./components/CardSearch.vue";
import QuestionCard from "./components/QuestionCard.vue";

export default {
  name: "card-art-quiz",
  data() {
    return {
      quizStarted: false,
      randomCardURL: "https://db.ygoprodeck.com/api/v7/randomcard.php",
      difficulty: "easy",
      currentQuestion: 1,
      currentCard: null,
      currentQuestionResult: "wrong",
      showCurrentQuestionResult: false,
      showQuestion: false,
      showFinishScreen: false,
      jokerUsed: false,
      jokersLeft: 3,
      score: 0,
      answer: ""
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
    getCardName(name) {
      this.answer = name;
    },
    evaluateAnswer() {
      if (this.currentCard.name.toLowerCase() === this.answer.toLowerCase()) {
        this.score++;
        this.currentQuestionResult = "right";
      }
      else {
        this.currentQuestionResult = "wrong";
      }
      
      this.answer = "";
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
    getImage(img) {
      return require("./assets/images" + img);
    }
  },
  computed: {
    getCardImage() {
      if (!this.currentCard) return "";
      return this.currentCard.card_images[0].image_url;
    },
    showHeader() {
      return this.quizStarted === false || 
             this.showCurrentQuestionResult === false && this.showFinishScreen === true ||
             this.showCurrentQuestionResult === false && this.showQuestion === true
    }
  },
  components: {
    CardSearch,
    QuestionCard
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
  background-color: hsl(220, 19%, 58%);
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

/* screens */

.screen {
  padding: 3em;
  background-color: rgba(0, 0, 10, 0.35);
  box-shadow: 0 0 8px rgba(0, 0, 10, 0.25),
              0 0 8px rgba(0, 0, 10, 0.25);
}

.start-screen {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

.question-screen {
  height: 75%;
  display: flex;
  justify-content: space-between;
  position: relative;
}

.finish-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
}

@media screen and (max-height: 47em) {
  .screen,
  .question-screen {
    height: 90%;
  }
}



.main-title {
  margin-bottom: 0.5em;
  font-size: 3.25rem;
  color: hsl(220, 95%, 90%);
  letter-spacing: 2px;
}

.intro {
  max-width: 45ch;
  margin-bottom: 1.5em;
  font-size: 1.15rem;
  color: #efeff5;
}

/* card image */

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

/* result screen */

.current-result-screen {
  display: flex;
  justify-content: space-between;
}

.card {
  width: 90%;
  max-width: 400px;
  align-self: center;
  box-shadow: 
    0 0 0.5em rgba(256, 256, 256, 0.5),
    0 0 1.75em rgba(256, 256, 256, 0.75);
}

.current-result-container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: auto;
}

.current-result {
  width: 100%;
  max-width: 575px;
  margin-top: 6em;
}

.scale-enter-active.current-result {
  animation: scale-in 0.5s ease-out;
  animation-delay: 0.4s;
}

.current-result-container .button {
  align-self: flex-end;
}

/* finish screen */

.score {
  margin-bottom: 1em;
  font-size: 2rem;
  color: #fff;
}

[v-cloak] { display: none; }

</style>

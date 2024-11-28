<template>
  <div>
      <section class="quiz-screen">
        <div class="flow">
          <div v-show="!showResultScreen" class="box box--light columns">
            <div class="card-image-container quiz-screen__section flow">

              <card-image v-show="currentCard !== null" 
                          :card="currentCard" :zoom-levels="zoomLevels" :zoom="currentZoomLevel"
                          ref="image">
              </card-image>

              <div class="zoom-buttons">
                <button v-for="(button, index) in nextZoomLevels"
                        :key="button" class="button--primary button--small" 
                        :disabled="index !== currentZoomLevel"
                        @click="nextZoom">
                  zoom {{index + 2}}
                </button>
                <button class="button--primary button--small" 
                        :disabled="currentZoomLevel !== zoomLevels.length-1"
                        @click="giveupCard">
                  Give up
                </button>
              </div>

              <p class="result-feedback box box--dark" 
                 :class="{'result-feedback--visible': showCurrentFeedback}">
                {{feedbackText}}
              </p>
            </div>

            <card-search @card-selected="checkAnswer" @close-menu="closeMenu" :show-card-search="showCardSearch">
            </card-search>
          </div>

          <result-screen v-show="showResultScreen" 
                           :card="currentCard" 
                           :result="currentResult"
                           :show-result="showResultScreen"
                           :zoom="currentZoomLevel"
                           :mode="mode"
                           @return-to-game="continueQuiz">
          </result-screen>

          <div class="quiz-data box box--dark">
            <p>
              <span>Card: {{currentCardNumber}}</span>
              <span v-show="mode !== 'time'"> / {{cardsPerRound}}</span>
            </p>
            <p>Score: {{score}}</p>
            <p>Guesses Left: {{guessesLeft}}</p>
            <div v-show="mode === 'time'">Time left: <game-timer ref="timer" @timer-ended="onTimeEnded"></game-timer></div>
            <button class="card-search__open button--primary button--small" @click="openMenu">Search</button>
          </div>
        </div>
      </section>
    </div>
</template>

<script>

import CardSearch from "./CardSearch.vue";
import CardImage from "./CardImage.vue";
import GameTimer from "./GameTimer.vue";
import ResultScreen from "./ResultScreen.vue";

export default {
  name: "QuizComponent",
  data() {
    return {
      randomCardApi: "https://db.ygoprodeck.com/api/v7/cardinfo.php?num=1&offset=0&sort=random&cachebust",
      quizIsRunning: false,

      mode: "normal",

      cardsPerRound: 10,
      
      score: 0,
      cardsGuessed: [],

      guessesLeft: 5,
      
      showCurrentFeedback: false,
      feedbackText: "",
      
      showCardSearch: false, // used for small screnn sizes
      
      showResultScreen: false,
      currentResult: false,
      
      currentCard: null,
      currentCardNumber: 0,
      
      currentZoomLevel: 0,
      zoomLevels: [3900, 1850, 1100, 650, 400],
      scoreMap: {
        "0": 100,
        "1": 75,
        "2": 50,
        "3": 25,
        "4": 10
      }
    }
  },
  methods: {
    startQuiz(mode) {
      this.setMode(mode);
      this.quizIsRunning = true;
      this.showResultScreen = false;
      this.cardsGuessed = [];
      this.currentCard = null;
      this.currentCardNumber = 0;
      this.currentZoomLevel = 0;
      this.score = 0;
      this.guessesLeft = 5;
      this.cardsPerRound = 10;
      
      this.nextCard();
      
    },
    endQuiz() {
      this.quizIsRunning = false;
      
      const quizResult = {
        score: this.score,
        cardsGuessed: this.cardsGuessed,
        mode: this.mode
      };

      this.$emit("quiz-end", quizResult);
    },
    setMode(mode) {
      this.mode = mode;

      if (mode === "time") {
        this.$refs.timer.runTimer();
        console.log("time mode")
      }
      
      this.zoomLevels = this.mode === "bonus" ? [2360, 1515, 1000, 640, 400] : [3900, 1850, 1100, 650, 400];
    },
    showCurrentResult() {
      this.quizIsRunning = false;
      
      if (this.mode === "time") this.$refs.timer.pauseTimer();
      
      this.showResultScreen = true;
    },
    continueQuiz(addBonusCard = false) {
      this.quizIsRunning = true;

      if (addBonusCard) this.cardsPerRound++;
      
      if (this.mode === "time") this.$refs.timer.resumeTimer();
      
      this.showResultScreen = false;
      this.nextCard();
    },
    getRandomCard() {
      if (!this.quizIsRunning) return;
      
      fetch(this.randomCardApi)
        .then(result => result.json())
        .then(result => {
          const card = result.data[0];
          
          // if the card is a skill card, token, match winner or not released fetch again
          const isMatchWinner = /you win the match|wins the match/g.test(card.desc.toLowerCase());
          
          if (card.type === "Skill Card" || card.id >= 100000000 || card.type === "Token" || isMatchWinner) {
            console.log("fetch again ")
            setTimeout(() => this.getRandomCard(), 150);
          }
          else {
            this.$refs.image.clear();
            this.currentCard = card;
            
            this.$nextTick(() => {
              this.$refs.image.nextImage();
            });
          }
        })
    },
    nextCard() {
      if (this.mode !== "time" && this.currentCardNumber === this.cardsPerRound) this.endQuiz();
      
      this.currentCard = null;
      this.currentZoomLevel = 0;
      this.currentCardNumber++;
      this.guessesLeft = 5;
      
      this.getRandomCard();
    },
    giveupCard() {
      this.addAnswerToList(false);
      this.currentResult = false;
      
      this.showCurrentResult();
    },
    nextZoom() {
      this.currentZoomLevel++;

      this.$nextTick(() => {
        this.$refs.image.drawImage();
      })
    },
    checkAnswer(name) {
      if (this.guessesLeft < 1) return;
      const result = name.toLowerCase() === this.currentCard.name.toLowerCase();
      this.currentResult = result;
      this.guessesLeft--;
      
      if (result === true) {
        this.score += this.scoreMap[this.currentZoomLevel];
        this.addAnswerToList(result);
        
        this.showCurrentResult();
      }
      else {
        if (this.guessesLeft === 0) {
          this.addAnswerToList(false);
          this.currentResult = false;
        }
        this.showFeedback("Incorrect!");
      }
      
    },
    addAnswerToList(result) {
      const answer = {
        card: this.currentCard,
        cardNumber: this.currentCardNumber,
        guessedRight: result,
        gainedPoints: result === true ? this.scoreMap[this.currentZoomLevel] : 0,
        guessesNeeded: 5 - this.guessesLeft
      };
      
      this.cardsGuessed.push(answer);
    },
    showFeedback(feedback) {
      this.feedbackText = feedback;
      this.showCurrentFeedback = true;
      setTimeout(() => {
        this.showCurrentFeedback = false;
        if (this.guessesLeft < 1) this.showCurrentResult();
      }, 1250);
    },
    onTimeEnded() {
      this.endQuiz();
    },
    closeMenu() {
      this.showCardSearch = false;
    },
    openMenu() {
      if (!this.quizIsRunning) return;
      
      this.showCardSearch = true;
    }
  },
  computed: {
    nextZoomLevels() {
      return this.zoomLevels.slice(1);
    }
  },
  components: {
    CardImage,
    GameTimer,
    CardSearch,
    ResultScreen
  }
}

</script>

<style scoped>

.quiz-screen {
  position: relative;
}

.quiz-screen__section {
  padding: var(--spacing-medium-3) var(--spacing-big-1);
}

@media screen and (max-width: 35em) {
  .quiz-screen__section {
    padding: var(--spacing-medium-1) var(--spacing-medium-1);
  }
}

.card-image-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.zoom-buttons {
  display: flex;
}

.zoom-buttons > * + * {
  margin-left: var(--spacing-small-2);
}

@media screen and (max-width: 35em) {
  .zoom-buttons {
    flex-direction: column;
  }
  .zoom-buttons > * + * {
    margin-left: 0;
    margin-top: var(--spacing-small-2);
  }

  .zoom-buttons button:disabled {
    display: none;
  }
}

.result-feedback {
  align-self: stretch;
  padding: var(--spacing-small-1);
  text-align: center;
  opacity: 0;
  transition: opacity 0.5s;
}

.result-feedback--visible {
  opacity: 1;
}

.quiz-data {
  display: flex;
  align-items: center;
  padding: 0.5rem;
  font-weight: 800;
}

.quiz-data > * + * {
  margin-left: 1rem;
}

.card-search__open {
  display: none;
}

@media screen and (max-width: 60em) {
  .card-search__open {
    display: inline-block;
  }
}

</style>

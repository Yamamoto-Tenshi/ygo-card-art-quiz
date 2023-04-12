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
                           @return-to-game="continueQuiz">
          </result-screen>

          <div class="quiz-data box box--dark">
            <p>Card: {{currentCardNumber}}</p>
            <p>Score: {{score}}</p>
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
      randomCardApi: "https://db.ygoprodeck.com/api/v7/randomcard.php",
      mode: "",
      quizIsRunning: false,
      
      score: 0,
      cardsGuessed: [],
      
      showCurrentFeedback: false,
      feedbackText: "",
      
      showCardSearch: false, // used for small screnn sizes
      
      showResultScreen: false,
      currentResult: false,
      
      currentCard: null,
      currentCardNumber: 0,
      
      currentZoomLevel: 0,
      zoomLevels: [4000, 2700, 1500, 800, 400],
      scoreMap: {
        "0": 100,
        "1": 75,
        "2": 50,
        "3": 25,
        "4": 2
      }
    }
  },
  methods: {
    startQuiz(mode) {
      this.mode = mode;
      this.quizIsRunning = true;
      this.showResultScreen = false;
      this.cardsGuessed = [];
      this.currentCard = null;
      this.currentCardNumber = 0;
      this.currentZoomLevel = 0;
      this.score = 0;
      
      this.nextCard();
      
      if (this.mode === "time") {
        this.$refs.timer.runTimer();
        console.log("time mode")
      }
      else if (this.mode === "normal") {
        console.log("normal mode")
      }
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
    showCurrentResult() {
      this.quizIsRunning = false;
      
      if (this.mode === "time") this.$refs.timer.pauseTimer();
      
      this.showResultScreen = true;
    },
    continueQuiz() {
      this.quizIsRunning = true;
      
      if (this.mode === "time") this.$refs.timer.resumeTimer();
      
      this.showResultScreen = false;
      this.nextCard();
    },
    getRandomCard() {
      if (!this.quizIsRunning) return;
      
      fetch(this.randomCardApi)
        .then(result => result.json())
        .then(result => {
          // if card is a skill card, token or not released fetch again
          if (result.type === "Skill" || result.id >= 100000000 || result.type === "Token") {
            console.log("fetch again")
            setTimeout(() => this.getRandomCard(), 150);
          }
          else {
            this.$refs.image.clear();
            this.currentCard = result;
            
            this.$nextTick(() => {
              this.$refs.image.nextImage();
              
            });
          }
        })
    },
    nextCard() {
      if (this.mode === "normal" && this.currentCardNumber === 10) this.endQuiz();
      
      this.currentCard = null;
      this.currentZoomLevel = 0;
      this.currentCardNumber++;
      
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
      const result = name.toLowerCase() === this.currentCard.name.toLowerCase();
      this.currentResult = result;
      
      if (result === true) {
        this.score += this.scoreMap[this.currentZoomLevel];
        this.addAnswerToList(result);
        
        this.showCurrentResult();
      }
      else {
        this.showFeedback("Incorrect!");
      }
      
    },
    addAnswerToList(result) {
      const answer = {
        card: this.currentCard,
        cardNumber: this.currentCardNumber,
        guessedRight: result,
        gainedPoints: result === true ? this.scoreMap[this.currentZoomLevel] : 0
      };
      
      this.cardsGuessed.push(answer);
    },
    showFeedback(feedback) {
      this.feedbackText = feedback;
      this.showCurrentFeedback = true;
      setTimeout(() => {this.showCurrentFeedback = false}, 1250);
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
  padding: var(--spacing-medium-1);
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
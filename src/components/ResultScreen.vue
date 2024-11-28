<template>
  <div class="result-screen box box--light">
      <div class="result-container">
        <div class="scene">
          <div class="card-image">
            <div class="card-image__back" :style="{backgroundImage: 'url(' + cardImage + ')'}"></div>
            <img class="card-image__front" src="../assets/images/cardback.jpg" />
          </div>
        </div>
        <div class="result">
          <transition name="zoom-fade">
            <p v-show="showResult" class="result__text">{{resultText}}</p>
          </transition>

          <div v-if="mode === 'bonus'">
            <div v-show="zoom === 0 && result === true" class="result__info">
              + 1 Card 
              <div class="result__info-image" 
                   :class="{'card-add-animation': showAnimation}" 
                   ref="card"
                   @animationend="returnAfterAnimation">
              </div>
            </div>
          </div>

          <button class="button--primary button--big" @click="returnToGame">Continue</button>
        </div>
      </div>
    </div>
</template>

<script>

export default {
  name: "ResultScreen",
  props: {
    card: Object,
    result: Boolean,
    showResult: Boolean,
    zoom: Number,
    mode: String
  },
  data() {
    return {
      showAnimation: false
    }
  },
  methods: {
    returnToGame() {
      if (this.mode === "bonus") {
        if (this.result === true && this.zoom === 0) {
          this.showAnimation = true;
        }
        else this.$emit("return-to-game", false);
      }
      else this.$emit("return-to-game", false);
    },
    returnAfterAnimation() {
      this.showAnimation = false;
      this.$emit("return-to-game", true);
    }
  },
  computed: {
    cardImage() {
      return this.card ? this.card.card_images[0].image_url : "";
    },
    resultText() {
      return this.result ? this.zoom === 0 ? "Perfect" : "Correct" : "Incorrect!";
    }
  }
}

</script>

<style scoped>

.result-screen {
  display: flex;
  justify-content: center;
  padding: var(--spacing-medium-2) var(--spacing-big-2);
}

.result-container {
  display: flex;
  align-items: center;
  text-align: center;
}

.result {
  padding: 0 var(--spacing-big-2);
}

.scene {
  perspective: 3000px;
}

.result-screen .card-image {
  width: 315px;
  height: 450px;
  position: relative;
  transform-style: preserve-3d;
  animation: flip 0.75s ease-out forwards;
  animation-delay: 0.25s;
}

.card-image__back,
.card-image__front {
  position: absolute;
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.card-image__back {
  transform: rotateY(-180deg);
  background-position: center;
  background-size: cover;
  
}


@keyframes flip {
  0% {transform: rotateY(0deg);}
  100% {transform: rotateY(-180deg);}
}

.result__text {
  margin-bottom: var(--spacing-small-2);
  font-size: var(--font-size-7);
  font-weight: 800;
  color: var(--highlight-color);
  text-shadow: 0px 0px 5px #000,
               0px 0px 5px #000,
               0px 0px 5px #000,
               0px 0px 5px #000,
               0px 0px 5px #000;
  text-transform: uppercase;
  transition-delay: 1s;
}

@media screen and (max-width: 60em) {
  .result-container {
    flex-direction: column;
  }
  
  .result__text {
    font-size: var(--font-size-6);
  }
}

.result__info {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: var(--spacing-small-2);
  font-size: var(--font-size-2);
}

.result__info-image {
  margin-left: 1rem;
  height: 35px;
  width: 25px;
  background-image: url("../assets/images/cardback_small.jpg");
  background-size: cover;
  position: relative;
}

.card-add-animation::after {

  content: "";
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  height: 35px;
  width: 25px;
  background-image: url("../assets/images/cardback_small.jpg");
  background-size: cover;
  animation: add-card 1.2s forwards;
}

@keyframes add-card {
  0% {}

  50% {
    opacity: 1;
    box-shadow: 0 0 22px rgba(255, 255, 255, 5),
                0 0 22px rgba(255, 255, 255, 5);
  }
  100% {
    opacity: 0;
    transform: scale(2.5);
    -webkit-filter: brightness(550);
    box-shadow: 0 0 22px rgba(255, 255, 255, 5),
                0 0 22px rgba(255, 255, 255, 5);
  }
}


</style>
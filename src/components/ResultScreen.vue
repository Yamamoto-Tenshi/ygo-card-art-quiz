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
    showResult: Boolean
  },
  methods: {
    returnToGame() {
      console.log(this.showResult)
      this.$emit("return-to-game");
    }
  },
  computed: {
    cardImage() {
      return this.card ? this.card.card_images[0].image_url : "";
    },
    resultText() {
      return this.result ? "Correct" : "Incorrect!";
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


</style>
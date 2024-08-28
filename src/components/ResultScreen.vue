<template>
  <div class="result-screen box box--light">
      <div class="result-container">
        <div>
          <transition name="slide-fade">
            <img v-show="showResult" :src="cardImage" class="card-image" />
          </transition>
        </div>
        <div class="result">
          <transition name="fade"  style="transition-delay: 0.75s">
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

.result-screen .card-image {
  max-width: 315px;
  height: auto;
}

.result__text {
  margin-bottom: var(--spacing-small-2);
  font-size: var(--font-size-7);
  font-weight: 800;
  color: var(--highlight-color);
  text-shadow: 0px 0px 10px #000,
               0px 0px 10px #000,
               0px 0px 10px #000;
  text-transform: uppercase;
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
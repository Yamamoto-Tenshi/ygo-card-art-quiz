<template>
  <div class="score-screen box box--dark">
      <div class="flow">
        <h2 class="">Quiz Result</h2>
        <p>Cards: {{quizResult.cardsGuessed.length}}</p>
        <p class="score">Final Score: <span class="score__number">{{quizResult.score}}</span></p>
        <ul class="cards-guessed">
          <li v-for="result in quizResult.cardsGuessed" :key="result.cardNumber" 
              class="cards-guessed__result">
            <img :src="result.card.card_images[0].image_url" />
            <p>Answered: {{result.guessedRight ? 'Right' : 'Wrong'}}</p>
            <p>Points: {{result.gainedPoints}}</p>
          </li>
        </ul>
        <button class="button--primary button--big" @click="returnToStartScreen">OK</button>
      </div>
    </div>
</template>

<script>

export default {
  name: "ScoreScreen",
  props: {
    quizResult: Object,
    mode: String
  },
  methods: {
    returnToStartScreen() {
      this.$emit("return-to-start");
    }
  }
}

</script>

<style scoped>

.score-screen {
  padding: var(--spacing-medium-2);
  text-align: center;
}

.score {
  font-weight: 100;
  font-size: var(--font-size-3);
}

.score__number {
  color: var(--text-color-accent);
  font-weight: 800;
}

.cards-guessed {
  display: flex;
  flex-wrap: wrap;
  align-content: center;
  justify-content: center;
}

.cards-guessed > * {
  padding: var(--spacing-medium-1);
}

.cards-guessed__result > img {
  max-width: 230px;
  margin-bottom: var(--spacing-small-1);
}

</style>
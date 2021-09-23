<template>
  <div class="current-result-screen container">
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
      <button @click="$emit('next-card')" class="button button--primary button--big">
        Next
      </button>
    </div>
  </div>
</template>

<script>

export default {
  name: "CurrentResultScreen",
  props: {
    currentCard: Object,
    currentQuestionResult: String,
    showCurrentQuestionResult: Boolean
  },
  methods: {
    getImage(img) {
      return require("../assets/images" + img);
    }
  },
  computed: {
    getCardImage() {
      if (!this.currentCard) return "";
      return this.currentCard.card_images[0].image_url;
    }
  }
}

</script>

<style scoped>

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

  @media screen and (max-width: 50em) {
    .current-result-screen {
      flex-direction: column;
      position: relative;
    }

    .current-result-container {
      width: 100%;
      position: absolute;
      top: 90%;
      left: 0;
      flex-direction: row;
      align-items: center;
      padding: 0.5em;
      background-color: rgba(0, 0, 10, 0.35);
    }

    .card {
      margin-top: -5em;
    }

    .current-result {
      max-width: 165px;
      margin-top: 0;
    }

    .current-result-container .button {
      align-self: center;
    }
  } 

</style>
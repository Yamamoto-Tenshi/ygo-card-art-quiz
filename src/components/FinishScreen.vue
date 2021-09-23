<template>
  <div class="finish-screen container screen">
    <p class="score">Score: {{score}} out of {{currentQuestion}}</p>
    <button class="button button--primary button--big" @click="$emit('play-again')">Play Again</button>
  </div>
</template>

<script>

export default {
  name: "FinishScreen",
  props: {
    score: Number,
    currentQuestion: Number
  }
}

</script>

<style scoped>

  .finish-screen {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .score {
    margin-bottom: 1em;
    font-size: 2rem;
    color: #fff;
  }

  @media screen and (max-width: 70em) {
     .finish-screen {
      justify-content: center;
    }
  }

</style>
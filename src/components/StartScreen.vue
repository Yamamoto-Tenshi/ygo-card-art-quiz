<template>
  <div class="start-screen screen container">
    <div class="intro">
      <h1 class="main-title" >YGO Card Art Quiz</h1>
      <p class="intro__text">
        In this game you have to guess which Yu-Gi-Oh! card you see on the screen based
        on the artwork. However, only a small part of the image is shown!
        Each round consists of 10 cards. how many can you guess?
      </p>
      <button class="button button--primary button--big"
              @click="$emit('start-quiz')">
        Play
      </button>
    </div>
    <img class="intro__image" src="../assets/images/start-image.jpg" />
  </div>
</template>

<script>

export default {
  name: "StartScreen"
}

</script>

<style scoped>

  .start-screen {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  .main-title {
    margin-bottom: 0.5em;
    font-size: 3.25rem;
    color: hsl(220, 95%, 90%);
    letter-spacing: 2px;
  }

  @media screen and (max-width: 35em) {
    .main-title {
      font-size: 2.75rem;
    }
  }

  .intro__text {
    max-width: 45ch;
    margin-bottom: 1.5em;
    font-size: 1.15rem;
    color: #efeff5;
  }

  @media screen and (max-width: 70em) {
    .intro {
      width: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      text-align: center;
    }

    .intro__image {
      display: none;
    }
  }

</style>

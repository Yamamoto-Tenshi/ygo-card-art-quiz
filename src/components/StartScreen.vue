<template>
  <div class="start-screen box box--light flow">
      <div class="start-screen__content">
        <div class="start-screen__intro flow">
          <h2>Test your knowledge!</h2>

          <p>
            In this game you have to guess which Yu-Gi-Oh! card you see on the screen based on the artwork. 
            However, you can only see a small fraction of the image, because it is zommed in. You can 
            zoom out, but the more you do, the less points you get.
          </p>

          <div class="mode-selection columns columns--even">
            <div class="start-screen__mode box box--dark flow">
              <h3>Time Mode</h3>
              <p>Try to guess as many cards as possible within <strong>10 minutes</strong>.</p>
              <button class="button--primary button--big" @click="startGame('time')">Play</button>
            </div>

            <div class="start-screen__mode box box--dark flow">
              <h3>Normal Mode</h3>
              <p>Out of 10 cards, how many can you guess right?</p>
              <button class="button--primary button--big" @click="startGame('normal')">Play</button>
            </div>
          </div>
        </div>

        <img 
          src="../assets/images/start-image.jpg"
          class="start-screen__image"
        />
      </div>
      <button @click="changeDesign" class="button--primary button--small">Change Design</button>
    </div>
</template>

<script>

export default {
  name: "StartScreen",
  methods: {
    startGame(mode) {
      this.$emit("game-start", mode);
    },
    changeDesign() {
      this.$emit("change-design");
    }
  }
}

</script>

<style scoped>

.start-screen {
  padding: var(--spacing-big-2);
}

.start-screen__content {
  display: flex;
  justify-content: space-between;
}

.start-screen__intro {
  max-width: 70ch;
}

.start-screen__image {
  align-self: stretch;
  margin-left: var(--spacing-medium-2);
}

.start-screen__mode {
  padding: var(--spacing-medium-2);
  text-align: center;
}

@media screen and (max-width: 60em) {
  .start-screen__image {
    display: none;
  }
}

</style>

<template>
    <div class="start-screen box box--light flow">
      <div class="start-screen__content">
        <div class="start-screen__intro flow">
          <h2>Test your knowledge!</h2>

          <p>
            In this game you have to guess which Yu-Gi-Oh! card you see on the screen based on the artwork. 
            However, you can only see a small fraction of the image, because it is zoomed in. You can 
            zoom out, but the more you do, the less points you get.
          </p>

          <form class="select-mode-section flow" @submit.prevent="startGame">
            <div class="flow" style="--flow-space: var(--spacing-small-1)">
              <div class="box box--dark"><p class="box__content">Select a Mode</p></div>

              <div class="mode-selection">
                <div class="mode-selection__options box box--light">
                  <p class="mode-selection__option">
                    <input type="radio" id="normal" name="mode" value="normal" v-model="mode" checked/>
                    <label for="normal">Normal Mode</label>
                  </p>

                  <p class="mode-selection__option">
                    <input type="radio" id="time" name="mode" value="time" v-model="mode" />
                    <label for="time">Time Mode</label>
                  </p>

                  <p class="mode-selection__option">
                    <input type="radio" id="bonus" name="mode" value="bonus" v-model="mode" />
                    <label for="bonus">Bonus Mode</label>
                  </p>
                </div>

                <div class="mode-selection__description box box--light">
                  <div class="box__content">
                    <p v-show="mode === 'normal'">
                      In this Mode you have to guess 10 Cards.
                    </p>
                    <p v-show="mode === 'time'">
                      Guess as many Cards as possible within 10 Minutes.
                    </p>
                    <p v-show="mode === 'bonus'">
                      Starting with 10 Cards to guess, every time you guess a card right 
                      on the first zoom, you get another Card to guess. The Zooms are
                      easier in this Mode.
                    </p>
                  </div>
                </div>
              </div>
            </div>
            <button class="button--primary">Play</button>
          </form>
        </div>

        <div class="start-screen__image"></div>
        
      </div>
      <!-- <button @click="changeDesign" class="button--primary button--small">Change Design</button> -->
    </div>
</template>

<script>

export default {
  name: "StartScreen",
  data() {
    return {
      mode: "normal"
    }
  },
  methods: {
    startGame() {
      this.$emit("game-start", this.mode);
    },
    changeDesign() {
      this.$emit("change-design");
    }
  }
}

</script>

<style scoped>
  
  .start-screen__content {
    display: flex;
  }

  .start-screen__intro {
    flex-basis: 65%;
    padding: 5rem 2rem;
  }



  .mode-selection {
    display: flex;
  }

  .mode-selection > * + * {
    margin-left: var(--spacing-small-1);
  }

  .mode-selection__options {
    flex-basis: 30%;
  }

  .mode-selection__description {
    flex-basis: 70%;
  }

  .mode-selection__option > input {
    display: none;
  }

  .mode-selection__option > label {
    display: block;
    font-weight: 800;
    padding: 0.25rem 0.5rem;
  }

  .mode-selection__option > input:checked + label {
    background-color: var(--highlight-color);
    animation: var(--highlight-animation);
  }

  @keyframes highlight-animation {
    0% {background-color: rgba(247, 163, 74, 1)}
    50% {background-color: rgba(247, 163, 74, 0.5)}
    100% {background-color: rgba(247, 163, 74, 1)}
  }

  .start-screen__image {
    flex-basis: 35%;
    background-image: url("../assets/images/start-image.jpg");
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center 25%;
  }


  @media screen and (max-width: 60em) {
    .start-screen__intro {
      flex-basis: 100%;
      padding: 1.5rem 1.5rem;
    }
    
    .mode-selection {
      flex-direction: column;
    }
    
    .mode-selection > * + * {
      margin-left: 0;
      margin-top: var(--spacing-small-1);
    }
    
    .start-screen__image {
      display: none;
    }
  }

</style>

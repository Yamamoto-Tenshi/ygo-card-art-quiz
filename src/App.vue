<template>
  <main id="app" >
  <div :class="{'design--5ds': showAltDesign}">
    <div class="header-wrapper">
      <header class="app-header">
        <h1 class="app-title">YGO Card Art Quiz</h1>
      </header>
    </div>

    <div class="wrapper">
      <start-screen v-show="showStartScreen" @game-start="play" @change-design="toggleDesign"></start-screen>

      <quiz-component v-show="showQuizScreen" 
                      ref="quizscreen"
                      @quiz-end="finishQuiz">
      </quiz-component>

      <score-screen v-show="showScoreScreen" 
                    :quiz-result="quizResult"
                    @return-to-start="returnToStartScreen">
      </score-screen>
    </div>
  </div>
</main>
</template>

<script>

import StartScreen from "./components/StartScreen.vue";
import QuizComponent from "./components/QuizComponent.vue";
import ScoreScreen from "./components/ScoreScreen.vue";

export default {
  name: "card-art-quiz",
  data() {
    return {
      showStartScreen: true,
      showQuizScreen: false,
      showResultScreen: false,
      showScoreScreen: false,
      
      quizResult: {},
      
      showAltDesign: false
    }
  },
  methods: {
    play(mode) {
      this.hideAllScreens();
      this.showQuizScreen = true;
      this.$refs.quizscreen.startQuiz(mode);
    },
    returnToStartScreen() {
      this.hideAllScreens();
      this.showStartScreen = true;
    },
    finishQuiz(quizResult) {
      this.quizResult = quizResult;
      this.hideAllScreens();
      this.showScoreScreen = true;
    },
    hideAllScreens() {
      this.showStartScreen = false;
      this.showQuizScreen = false;
      this.showResultScreen = false;
      this.showScoreScreen = false;
    },
    toggleDesign() {
      console.log(this.showAltDesign)
      this.showAltDesign = !this.showAltDesign;
    }
  },
  computed: {

  },
  components: {
    StartScreen,
    QuizComponent,
    ScoreScreen
  }
}
</script>

<style>

:root {
  --text-color-dark: #000;
  --text-color-light: #fff;
  --text-color-accent: #f7f794;
  
  --font-size-1: 0.875rem;
  --font-size-2: 1rem;
  --font-size-3: 1.25rem;
  --font-size-4: 1.75rem;
  --font-size-5: 2rem;
  --font-size-6: 2.5rem;
  --font-size-7: 5rem;
  
  --app-bg-color: #124232;
  
  --box-bg-dark: #534a27;
  --box-bg-medium: #cdc093;
  --box-bg-light: #e6e1cf;
  
  --border-color-dark: #c3b174;
  --border-color-medium: #9d9374;
  --border-color-light: #8fb869;
  
  --dark-box-shadow: inset 0px 0px 1px 2px #554416;
  --light-box-shadow: inset 0px 0px 1px 2px #b1a06a;
  
  --highlight-color: #f7a34a;
  
  --spacing-small-1: 0.25rem;
  --spacing-small-2: 0.5rem;
  --spacing-medium-1: 0.75rem;
  --spacing-medium-2: 1rem;
  --spacing-medium-3: 1.5rem;
  --spacing-big-1: 2rem;
  --spacing-big-2: 3rem;
  
}

.design--5ds {
  --app-bg-color: #66667f;
  
  --box-bg-dark: #565656;
  --box-bg-medium: #7e7e7e;
  --box-bg-light: #e0e1e3;
  
  --border-color-dark: #272624;
  --border-color-medium: #393832;
  --border-color-light: #393832;
  
  --dark-box-shadow: inset 0px 0px 1px 2px #868789;
  --light-box-shadow: inset 0px 0px 1px 2px #8f8e88;
}

/* Reset / defaults */

*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  min-height: 100vh;
  padding: var(--spacing-medium-2) 0;
  line-height: 1.5;
  font-family: "Segoe UI", sans-serif;
  font-size: var(--font-size-1, 0.875rem);
  color: var(--text-color-primary);
  overflow-x: hidden;
  background-color: var(--app-bg-color);
}

ul[class],
ol[class] {
  list-style: none;
}

img,
picture {
  max-width: 100%;
  display: block;
}

a {
  text-decoration: none;
  color: hsl(220, 70%, 45%);
}

input,
button,
textarea,
select {
  font: inherit;
}

button {
  cursor: pointer;
}

/* Global  */

h2,
h2,
h3,
h4 {
  line-height: 1.2;
  color: var(--text-color-secondary);
}

h1 {
  line-height: 1;
  font-size: var(--font-size-5);
  color: var(--header-color);
}

h2 {
  font-size: var(--font-size-4);
}

h3 {
  font-size: var(--font-size-3);
}

p {
  font-size: var(--font-size-2);
}

ul {
  font-size: 17px;
}

fieldset {
  border: none;
}

input[type="text"],
input[type="email"],
input[type="password"] {
  padding: 8px 16px;
  border: 1px solid #aaa;
}


[v-cloak] { display: none; }

/* utilities */

.wrapper {
  padding: 0 1.5rem;
  max-width: 70rem;
  margin: 0 auto;
}

.columns {
  --column-gap: var(--spacing-medium-1);
  
  display: flex;
}

.columns > * + * {
  margin-left: var(--column-gap);
}

.columns--even > * {
  flex: 1;
}

@media screen and (max-width: 60em) {
  .columns {
    flex-direction: column;
  }
  
  .columns > * + * {
    margin-top: var(--column-gap);
    margin-left: 0;
  }
}

.flow {
  --flow-space: var(--spacing-medium-1);
}

.flow > * + * {
  margin-top: var(--flow-space, 0.75rem);
}

.text-center {
  text-align: center;
}

/* global components */

.button--primary {
  --button-bg: linear-gradient(180deg, rgba(239,240,215,1) 15%, rgba(207,207,155,1) 45%);
  --button-color: #000;
  --button-border-color: #a6a682;
  
  padding: 0.25em 2em;
  color: var(--button-color);
  background-image: var(--button-bg);
  border-radius: 9999px;
  border: 2px solid var(--button-border-color);
  font-size: 1.05rem;
  text-transform: uppercase;
  font-weight: 800;
  box-shadow: inset 6px 7px 5px rgba(239,240,215,1),
              inset -2px 0px 5px rgba(239,240,215,1);
}

.button--primary:disabled {
  -webkit-filter: brightness(80%);
  filter: brightness(80%);
  opacity: 0.5;
  color: #666;
}

.button--primary:not(:disabled):hover,
.button--primary:not(:disabled):focus {
  --button-bg: linear-gradient(180deg, rgba(209,235,240,1) 15%, rgba(120,172,250,1) 45%);
  --button-border-color: #769db9;

  box-shadow: inset 6px 7px 5px rgba(209,235,240,1),
              inset -2px 0px 5px rgba(209,235,240,1),
              0px 0px 1px 3px #f7a34a;
}

.button--big {
  padding: 0.35em 2.25em;
  font-size: 1.15rem;
}

.button--small {
  padding: 0.05em 1em;
  font-size: 15px;
  text-transform: capitalize;
}

.box {
  border: 3px solid var(--box-border-color);
  border-radius: 4px;
  background-color: var(--box-bg-color);
  color: var(--box-text-color);
}

.box--light {
  --box-border-color: var(--border-color-light);
  --box-bg-color: var(--box-bg-light);
  --box-text-color: var(--text-color-dark);
  
  box-shadow: var(--light-box-shadow);
}

.box--medium {
  --box-border-color: var(--border-color-medium);
  --box-bg-color: var(--box-bg-medium);
  --box-text-color: var(--text-color-dark);
}

.box--dark {
  --box-border-color: var(--border-color-dark);
  --box-bg-color: var(--box-bg-dark);
  --box-text-color: var(--text-color-light);
  
  box-shadow: var(--dark-box-shadow);
}

/* header */

.header-wrapper {
  padding: 0 1.5rem;
  max-width: 75rem;
  margin: 0 auto;
}

.app-header {
  --header-color: var(--text-color-accent);
  padding: 0.75em;
  margin-bottom: var(--spacing-medium-1);
  text-align: center;
  color: var(--header-color);
  border-radius: 9999px;
  border: 3px solid #ccc;
  border-left-width: 4px;
  border-right-width: 4px;
}

/* animation / transition */

.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}

/* 5ds design */


.design--5ds .button--primary {
  /* outer border */
  --button-shape: polygon(6% 0%, 94% 0%, 100% 50%, 94% 100%, 6% 100%, 0% 50%);
  padding: 0.5em 1.75em;
  position: relative;
  color: #fff;
  font-size: 1.05rem;
  text-transform: uppercase;
  text-shadow: 0px 0px 2px #000,
               0px 0px 2px #000;
  border: none;
  box-shadow: 0px 0px 0px #fff;
  border-radius: 0px;
  border-bottom: 1px solid #5d5958;
  -webkit-clip-path: var(--button-shape);
  clip-path: var(--button-shape);
  background-color: rgba(86,81,78,1);
  background: linear-gradient(180deg, rgba(200, 196, 195, 1) 25%, rgba(86,81,78,1) 50%, rgba(200, 196, 195, 1) 75%);
}

.design--5ds .button--primary::after {
  /* inner border */
  --border-width: 4px;
  --border-width-double: 8px;
  content: "";
  position: absolute;
  top: var(--border-width);
  left: var(--border-width);
  width: calc(100% - var(--border-width-double));
  height: calc(100% - var(--border-width-double));
  background-color: #5d5958;
  -webkit-clip-path: var(--button-shape);
  clip-path: var(--button-shape);
  z-index: -2;
}

.design--5ds .button--primary::before {
  /* bg color */
  --border-width: 8px;
  --border-width-double: 16px;
  content: "";
  position: absolute;
  top: var(--border-width);
  left: var(--border-width);
  width: calc(100% - var(--border-width-double));
  height: calc(100% - var(--border-width-double));
  background: linear-gradient(180deg, rgba(204,199,200,1) 33%, rgba(97,91,90,1) 64%);
  -webkit-clip-path: var(--button-shape);
  clip-path: var(--button-shape);
  z-index: -1;
}

.design--5ds .button--primary:not(:disabled):hover {
  box-shadow: none;
}

.design--5ds .button--primary:not(:disabled):focs {
  box-shadow: none;
}

.design--5ds .button--primary:not(:disabled):hover::before,
.design--5ds .button--primary:not(:disabled):focus::before {
  background: linear-gradient(180deg, rgba(242,211,145,1) 33%, rgba(156,113,12,1) 64%);
}

.design--5ds .button--small {
  padding: 0.5em 1.75em;
  font-size: 14px;
}

.design--5ds .button--big {
  padding: 0.5em 2.25em;
  font-size: 1.5rem;
}

.design--5ds .app-header {
  --header-color: #f5f5f5;
  padding: 0.5rem;
  margin-bottom: var(--spacing-medium-1);
  border-left-width: 0px;
  border-right-width: 0px;
  border: 2px solid #989898;
  border-radius: 0px;
  background-image: linear-gradient(180deg, rgba(180,180,180,1) 40%, rgba(69,69,69,0.5) 49%);
  text-transform: uppercase;
  text-align: center;
  text-shadow: 0px 0px 5px #434041,
               0px 0px 5px #434041,
               0px 0px 5px #434041,
               0px 0px 5px #434041;
}

.design--5ds .card-search input[type="text"] {
  color: var(--text-color-light);
}


</style>

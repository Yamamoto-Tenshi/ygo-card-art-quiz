<template>
  <div>
    <div class="card-image"
         :style="{
            backgroundSize: bgSize,
            backgroundImage: setImage,
            backgroundPosition: setPosition
          }">
    </div>
  </div>
</template>

<script>
export default {
  name: "QuestionCard",
  props: {
    card: Object,
    joker: Boolean,
    difficulty: String
  },
  data() {
    return {
      imgURL: "https://images.ygoprodeck.com/images/cards_cropped/",
      zoom: {
        easy: {imageZoom: 300, jokerZoom: 90},
        medium: {imageZoom: 415, jokerZoom: 150},
        hard: {imageZoom: 520, jokerZoom: 200},
      }
    }
  },
  computed: {
    setImage() {
      return "url(" + this.imgURL + this.card.id + ".jpg)";
    },
    bgSize() {
      return this.zoom[this.difficulty].imageZoom - 
        (this.joker === false ? 0 : this.zoom[this.difficulty].jokerZoom) + "%";
    },
    setPosition() {
      return this.randomPosition() + this.randomPosition();
    }
  },
  methods: {
    randomPosition() {
      return Math.floor(Math.random() * 100) + "%";
    }
  }
}
</script>

<style scoped>

  .card-image {
    width: 345px;
    height: 345px;
    margin-bottom: 0.75em;
    transition: background-size 0.5s;
    transform: scale(0.1);
    opacity: 0;
    animation: showCard 0.5s forwards ease-out;
    animation-delay: 0.2s;
  }

  @media screen and (max-width: 25em) {
    .card-image {
      width: 92%;
      height: auto;
      margin-right: auto;
      margin-left: auto;
      aspect-ratio: 1 / 1;
    }
  }

  @keyframes showCard {
    0% {
      transform: scal(0.1);
      opacity: 0;
    }
    75% {
      opacity: 0.75;
    }
    100% {
      transform: scale(1);
      opacity: 1;
    }
  }

</style>
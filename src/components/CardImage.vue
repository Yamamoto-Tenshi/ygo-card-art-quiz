<template>
  <div>
      <div class="card-image" 
           :style="{backgroundSize: zoomLevel, backgroundImage: image, backgroundPosition: position}">
      </div>
    </div>
</template>

<script>

export default {
  name: "CardImage",
  props: {
    card: Object,
    zoom: Number,
    zoomLevels: Array
  },
  data() {
    return {
      imageURL: "https://images.ygoprodeck.com/images/cards_cropped/",
    }
  },
  computed: {
    zoomLevel() {
      return this.zoomLevels[this.zoom] + "%";
    },
    position() {
      return this.randomPosition() + this.randomPosition();
    },
    image() {
      if (this.card === null) return;
      return `url(${this.imageURL}${this.card.id}.jpg)`
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
  width: 350px;
  height: 350px;
}

@media screen and (max-width: 35em) {
  .card-image {
    width: 285px;
    height: 285px;
  }
}

</style>
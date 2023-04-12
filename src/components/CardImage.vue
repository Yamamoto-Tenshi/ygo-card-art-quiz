<template>
  <div>
    <canvas class="card-image" id="canvas" width="400" height="400" ref="canvas" ></canvas>
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
      ctx: null,
      baseSize: 0,
      x: 0,
      y: 0,
      image: null
    }
  },
  computed: {
    
  },
  methods: {
    zoomLevel() {
      return this.zoomLevels[this.zoom];
    },
    offset() {
      return this.baseSize / 2;
    },
    drawImage() {
      this.clear();
      
      let x = this.xPosition(),
          y = this.yPosition(),
          height = this.calculateHeight(),
          width = this.zoomLevel();

      this.ctx.drawImage(this.image, x, y, width, height);
    },
    clear() {
      this.ctx.clearRect(0, 0, this.baseSize, this.baseSize);
    },
    calculateHeight() {
      let increase = (this.zoomLevel()) * 100 / this.image.width;
      return this.image.height * increase / 100;
    },
    xPosition() {
      let result = (this.zoomLevel() / 2) + ((this.zoomLevel() / 2) - this.offset()) * this.x - this.offset();
      return -result;
    },
    yPosition() {
      let result = (this.zoomLevel() / 2) + ((this.zoomLevel() / 2) - this.offset()) * this.y - this.offset();
      return -result;
    },
    getRandomPositions() {
      this.x = (Math.random() * 1 * (Math.round(Math.random()) ? 1 : -1)).toFixed(2);
      this.y = (Math.random() * 1 * (Math.round(Math.random()) ? 1 : -1)).toFixed(2);
    },
    nextImage() {
      this.getRandomPositions();
      this.image.src = this.card !== null ? `${this.imageURL}${this.card.id}.jpg` : "";
    }
  },
  mounted() {
    this.image = new Image();
    this.ctx = this.$refs.canvas.getContext("2d");
    this.baseSize = this.$refs.canvas.width;

    this.image.onload = () => {
      this.drawImage();
    }
    
  }
}
</script>

<style scoped>

@media screen and (max-width: 35em) {
  .card-image {
    width: 285px;
    height: 285px;
  }
}

</style>
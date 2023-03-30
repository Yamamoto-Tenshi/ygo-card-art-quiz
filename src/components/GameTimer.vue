<template>
  <span>{{formatedTime}}</span>
</template>

<script>

export default {
  name: "GameTimer",
  props: {
    timeLimit: Object
  },
  data() {
    return {
      minutes: 10,
      seconds: 0,
      timerInterval : null
    }
  },
  methods: {
    startTimer() {
      this.minutes = this.timeLimit.minutes;
      this.seconds = this.timeLimit.seconds;
      this.runTimer();
    },
    pauseTimer() {
      clearInterval(this.timerInterval)
    },
    resumeTimer() {
      this.runTimer();
    },
    runTimer() {
      console.log("time...")
      clearInterval(this.timerInterval);

      this.timerInterval = setInterval(() => {

        if (this.seconds === 0 && this.minutes !== 0) {
          this.minutes--;
        }
        
        if (this.seconds === 0) {
          this.seconds = 59;
        }
        else this.seconds--;
        
        if (this.minutes === 0 && this.seconds === 0) {
          clearInterval(this.timerInterval);
          
          this.$emit("timer-ended");
        }

      }, 1000);
    }
  },
  computed: {
    formatedTime() {
      return (this.minutes < 10 ? "0" + this.minutes : this.minutes) + ":" + 
      (this.seconds < 10 ? "0" + this.seconds : this.seconds)  
    }
  }
}

</script>

<style scoped>

</style>

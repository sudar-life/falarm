<template>
  <div class="timer-view-container" :class="background">
    <div class="time-box">
      <p v-text="pmamString"></p>
      <span v-text="hourString"></span>
      <span>:</span>
      <span v-text="minutesString"></span>
      <span class="seconds" v-text="secondsString">26</span>
    </div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      hour: 0,
      minutes: 0,
      seconds: 0
    };
  },
  props: {
    active: Boolean
  },
  mounted: function() {
    this.init();
    this.run();
  },
  computed: {
    background: function() {
      return this.active ? "red" : "normal";
    },
    hourString: function() {
      return this.hour < 10 ? "0" + this.hour : this.hour;
    },
    minutesString: function() {
      return this.minutes < 10 ? "0" + this.minutes : this.minutes;
    },
    secondsString: function() {
      return this.seconds < 10 ? "0" + this.seconds : this.seconds;
    },
    pmamString: function() {
      return this.hour > 12 ? "PM" : "AM";
    }
  },
  methods: {
    init: function() {
      setInterval(this.run, 1000);
    },
    run: function() {
      this.hour = new Date().getHours();
      this.minutes = new Date().getMinutes();
      this.seconds = new Date().getSeconds();
    }
  }
};
</script>

<style>
@import "../assets/css/components/timer-view-pannel.css";
</style>

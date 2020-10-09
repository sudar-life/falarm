<template>
  <div id="app">
    <setting-pannel
      style="background-color:#fff"
      v-on:actionalarm="actionAlarm"
    />
    <timer-view-pannel v-bind:active.sync="active" />
  </div>
</template>
<script>
import SettingPannel from "./components/SettingPannel";
import TimerViewPannel from "./components/TimerViewPannel";
export default {
  data: function() {
    return {
      audio: null,
      active: false
    };
  },
  mounted: function() {
    this.audio = new Audio(
      "http://soundbible.com/mp3/Air Plane Ding-SoundBible.com-496729130.mp3"
    );
  },
  components: { SettingPannel, TimerViewPannel },
  methods: {
    actionAlarm: function() {
      if (this.audio != null) {
        this.audio.play();
      }
      this.active = true;
      setTimeout(this.reset, 1000);
    },
    reset: function() {
      this.active = false;
    }
  }
};
</script>
<style>
@import "./assets/css/style.css";
</style>

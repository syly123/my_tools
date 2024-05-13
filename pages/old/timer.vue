<template>
  <div>
    <p>
      <input v-model.number="waitMinute" />分<input
        v-model.number="waitSecond"
      />秒
    </p>
    <button @click="setTimer()">アラームセット</button>
    <h2>アラームセット中か→{{ alarmAlreadySet }}</h2>
  </div>
</template>

<script>
export default {
  name: "player",
  props: {
    src: String,
  },
  data: function () {
    return {
      audio: new Audio(
        "http://soundbible.com/mp3/analog-watch-alarm_daniel-simion.mp3"
      ),
      isPlay: false,
      duration: 0,
      currentTime: 0,
      waitMinute: 1,
      waitSecond: 30,
      alarmAlreadySet: false,
      alarm: null,
    };
  },
  methods: {
    play: function () {
      let audio = this.audio;
      audio.volume = 0.3;
      audio.play();
      window.setTimeout(function () {
        audio.pause();
      }, 2000);
    },
    setTimer: function () {
      clearTimeout(this.alarm);
      let t = this;
      this.alarmAlreadySet = true;
      this.alarm = window.setTimeout(function () {
        t.play();
        t.alarmAlreadySet = false;
      }, this.waitTime * 1000);
    },
  },
  computed: {
    waitTime: function () {
      return this.waitMinute * 60 + this.waitSecond;
    },
  },
};
</script>

<style scoped></style>

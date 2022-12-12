<template>
  <div class="box">
    <button @click="setTimer()">setitime</button>
    {{ elapsedTime }}秒 / {{ count + 1 }}問目
    <div>
      <!-- <a :href="worker[photoIndex]" target="_blank">画像</a> -->
      <div v-if="show_answer" style="font-weight: 800; font-size: 80px">
        {{ answers_js.tamatebako_yomitori[count] }}
      </div>
      <img v-if="!show_answer" :src="img_url" height="600px" />
    </div>
  </div>
</template>
<script>
import answers_js from "~/assets/answers.js";
export default {
  data() {
    return {
      count: 0,
      show_answer: false,
      img_count: 0,
      answers_js: answers_js,
      stopwatch: null,
      startTime: 0,
      elapsedTime: 0,
    };
  },
  methods: {
    setTimer: function () {
      this.startTime = Date.now();
      this.stopwatch = setInterval(() => {
        this.elapsedTime = Math.trunc((Date.now() - this.startTime) * 0.001);
      }, 1000); // 10msごとに現在時刻とstartを押した時刻の差を足す
    },

    addCount: function (num) {
      this.count += num;
      this.show_answer = false;
      this.startTime = Date.now();
      if (this.count == -1) {
        this.count = 0;
      }
    },
    keyCheck: function (event) {
      if (event.defaultPrevented) {
        return;
      }
      switch (event.key) {
        case "ArrowLeft":
          this.addCount(-1);
          break;
        case "ArrowRight":
          this.addCount(1);
          break;
        case "ArrowUp":
          this.show_answer = !this.show_answer;
          break;
        case "ArrowDown":
          this.show_answer = !this.show_answer;
          break;
        default:
          return;
      }
      event.preventDefault();
    },
  },
  computed: {
    img_url: function () {
      try {
        return require("@/assets/webtest_imgs/webtest" +
          this.count.toString() +
          ".png");
      } catch (err) {
        this.addCount(-1);
        return "https://p.e-words.jp/img/Error.png";
      }
    },
  },
  created: function () {},
  mounted: function () {
    //createdのイベントだとまだ要素にアクセスできない状態なので、初期フォーカスをしたい場合はmountedのイベントで行う必要があります。
    window.addEventListener("keydown", this.keyCheck);
  },

  beforeDestroy() {
    clearInterval(this.stopwatch);
    window.removeEventListener("keydown", this.keyCheck);
  },
};
</script>
<style>
/* .container {
  margin: 30%;
  text-align: center;
  background-color: #fff2fa;
} */

.box {
  /* position: absolute; 子要素をabusoluteに */
  text-align: center;
  margin: 0; /*margin:0を入れる*/
  padding: 10px; /*左右のpaddingを0にする*/
}
</style>

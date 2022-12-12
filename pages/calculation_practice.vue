<template>
  <div class="box">
    <div>
      {{ question }}
    </div>
    <div>
      <!-- <a :href="worker[photoIndex]" target="_blank">画像</a> -->
      <img src="" />
    </div>

    <input ref="answer_input" v-model="answer" />
  </div>
</template>
<script>
import spiQs from "~/assets/spiQs.json";
export default {
  head() {
    return {
      script: [
        {
          src: "https://cdn.jsdelivr.net/npm/vuetify@2.x/dist/vuetify.js",
        },
      ],
      // link: [
      //   {
      //     rel: "stylesheet",
      //     href: "https://fonts.googleapis.com/css?family=Roboto",
      //   },
      // ],
    };
  },
  data() {
    return {
      spiQs: spiQs,
      answer: "",
      answer_key: "",
      is_correct: false,
      mode: "interview",
      img_url: "",
      count: 0,
    };
  },
  methods: {
    safeEval: function (val) {
      return Function('"use strict";return (' + val + ")")();
    },
    randomInt: function (min = 2, max = 99) {
      return Math.floor(Math.random() * (max + 1 - min)) + min;
    },
    submit_answer: function () {
      if (this.answer == this.answer_key) {
        is_correct = true;
      } else {
        is_correct = false;
      }
    },
    addCount: function (num) {
      this.count += num;
      if (this.count == -1) {
        this.count = 0;
      } else if (this.count > this.splitted_sentence_list.length - 2) {
        this.count = this.splitted_sentence_list.length - 2;
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
        default:
          return;
      }
      event.preventDefault();
    },
  },
  computed: {
    question: function () {
      let ques = this.spiQs[this.count].question;
      let ans = this.spiQs[this.count].answer;
      console.log(ques);

      let alphabets = ques[this.count].match(/\[(.+)\]/g);
      console.log(alphabets);

      for (let i = 0; i < alphabets?.length; i++) {
        const el = alphabets[i];
        if (el.includes("~")) {
          el = el.split(",");
          let alphabet = el[0];
        }
      }

      ans = this.safeEval(ans);
      let [num1, num2] = [this.randomInt(2, 9), this.randomInt()];
      this.answer_key = (num1 * num2).toString();
      return num1 + "  ×  " + num2;
      //https://lab.syncer.jp/Web/JavaScript/Snippet/15/
    },
  },
  mounted: function () {
    //createdのイベントだとまだ要素にアクセスできない状態なので、初期フォーカスをしたい場合はmountedのイベントで行う必要があります。
    window.addEventListener("keydown", this.keyCheck);
  },

  beforeDestroy() {
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

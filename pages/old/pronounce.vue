<template>
  <div class="box">
    <br />
    <input v-model="word_list" />
    <button @click="pronounce">♪</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      sentence: "",
      count: 0,
      word_list: "",
      speaking: false,
      timer: [],
    };
  },
  methods: {
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
    pronounce: function () {
      if (!"SpeechSynthesisUtterance" in window) {
        alert("Speech synthesis(音声合成) APIには未対応です.");
        return;
      }
      let word_list = this.word_list.split(" ");
      console.log(word_list);
      //   if (!speechSynthesis.speaking) {
      if (!this.speaking) {
        let u = new SpeechSynthesisUtterance();
        let voices = window.speechSynthesis.getVoices();
        u.voice = voices.find(function (voice) {
          return voice.name === "Google US English";
        });
        u.lang = "en-US";
        // u.addEventListener("end", (e) => {
        //   this.speaking = false;
        // });

        word_list.forEach((word, i) => {
          this.speaking = true;
          this.timer[i] = setTimeout(function () {
            console.log(word);
            u.text = word;
            speechSynthesis.speak(u);
            this.speaking = true;
          }, i * 2000);
        });
      } else {
        speechSynthesis.cancel();
        for (var i = 0; i < this.timer.length; i++) {
          clearTimeout(this.timer[i]);
        }
        this.speaking = false;
        console.log("読み上げをキャンセルしました");
      }
    },
  },
  computed: {
    splitted_sentence_list: function () {
      this.count = 0;
      if (this.sentence != "") this.sentence += ".";
      return this.sentence.split(/、|。|,|\/|\./g); //|,|.|:
    },
    sentence_show: function () {
      return this.splitted_sentence_list[this.count];
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
* {
  margin: 0;
}
.box {
  /* position: absolute; 子要素をabusoluteに */
  text-align: center;
  margin: 0; /*margin:0を入れる*/
}
</style>

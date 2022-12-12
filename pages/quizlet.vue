<template>
  <div class="box">
    <input type="file" @change="loadHtmlFile" />
    <button @click="shuffled = !shuffled">シャッフル</button>
    {{ shuffled ? "オン" : "オフ" }}
    <div v-html="question[index]" style="margin: 1% 0"></div>
    <button @click="addCount(-1)">←</button>
    <button @click="answer_visible = !answer_visible">Check Answer</button>
    <button @click="addCount(1)">→</button>
    <div @click="favIndex.splice(index, 1, favIndex[index] * -1)">
      {{ favIndex[index] == -1 ? "★" : "☆" }}
    </div>
    <!-- style="width: 100%; height: 270px" -->

    <div v-show="answer_visible" v-html="answer[index]"></div>

    <!-- {{ shuffledIndex }}{{ answer.length }} -->
    <!-- <div>{{ q_and_a }}</div> -->

    <!-- {{ "favindex:" + favIndex }} -->
    <!-- <button @click="copyToClipboard()">エクスポート</button> -->
  </div>
</template>

<script>
//import spreadsheet_data from "~/assets/spreadsheet_data.json";
export default {
  head() {
    return {
      // script: [
      //   {
      //     src: "https://cdn.jsdelivr.net/npm/vuetify@2.x/dist/vuetify.js",
      //   },
      // ],
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
      spreadsheet_data: "",
      answer: [],
      question: [],
      answer_visible: false,
      shuffled: false,
      // question2: "",
      count: 0,
      index: 0,
      favIndex: [],
    };
  },
  methods: {
    shuffle: function (arr) {
      for (let i = arr.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    },

    addCount: function (num) {
      this.count += num;
      if (this.shuffled) {
        this.index = this.shuffledIndex[this.count];
      } else {
        this.index = this.count;
      }
      this.answer_visible = false;
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
        case " ":
          this.answer_visible = !this.answer_visible;
          break;
        default:
          return;
      }
      event.preventDefault();
    },
    loadHtmlFile(e) {
      let vm = this;
      let file = e.target.files[0];

      let reader = new FileReader();
      reader.readAsText(file);

      reader.onload = () => {
        let lines = reader.result;
        this.spreadsheet_data = lines;
        //console.log(lines);
        let sentence = this.spreadsheet_data;

        //以下の処理はonload内に書かないとhtml読み込み完了前に実行されるので注意
        sentence = sentence.split(/<\/td>|<td.+?>/);
        //console.log(sentence);
        let qa_flag = true;
        for (let i = 0; i < sentence.length; i++) {
          const el = sentence[i];
          if ((el[0] == "<" && el[1] != "s") || el == "") {
          } else {
            qa_flag ? this.question.push(el) : this.answer.push(el);
            qa_flag = !qa_flag;
          }
        }
        this.favIndex = Array(this.question.length);
        this.favIndex.fill(1);
        console.log("question:", this.question);
        console.log("answer:", this.answer);
      };
    },
    copyToClipboard: function () {
      // コピー対象をJavaScript上で変数として定義する
      var copyTarget = document.getElementById("copyTarget");

      // コピー対象のテキストを選択する
      copyTarget.select();

      // 選択しているテキストをクリップボードにコピーする
      document.execCommand("Copy");

      // コピーをお知らせする
      //alert("コピーできました！ : " + copyTarget.value);
    },
  },
  computed: {
    // q_and_a: function () {
    //   let sentence = this.spreadsheet_data;
    //   //console.log(sentence);
    //   sentence = sentence.split(/<\/td>|<td.+?>/);
    //   //console.log(sentence);
    //   let qa_flag = true;
    //   for (let i = 0; i < sentence.length; i++) {
    //     const el = sentence[i];
    //     if ((el[0] == "<" && el[1] != "s") || el == "") {
    //     } else {
    //       qa_flag ? this.question.push(el) : this.answer.push(el);
    //       qa_flag = !qa_flag;
    //     }
    //   }
    //   this.favIndex = Array(this.question.length);
    //   this.favIndex.fill(false);
    //   console.log("question:", this.question);
    //   console.log("answer:", this.answer);
    // },

    shuffledIndex: function () {
      let originIndex = [...Array(this.answer.length).keys()];
      return this.shuffle(originIndex);
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

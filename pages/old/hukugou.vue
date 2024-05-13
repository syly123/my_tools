<template>
  <div class="box">
    <div style="margin: 50px 40% 50px; font-size: 80%">
      <textarea id="copyTarget" type="text" :value="hukugou_str" readonly />
      <button @click="copyToClipboard()">コピー</button>
    </div>
    <div style="margin: 0px 40% 30px; font-size: 80%">
      {{ hukugou_str }}
    </div>

    <textarea v-model="angou" />
  </div>
</template>
<script>
export default {
  data() {
    return {
      sentence: "",
      angou: "",
      count: 0,
    };
  },
  methods: {
    addCount: function (num) {
      this.count += num;
      if (this.count == -1) {
        this.count = 0;
      } else if (this.count > this.splitted_sentence_list.length - 2) {
        this.count = this.splitted_sentence_list.length - 2;
      }
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
    angouka: function (str) {
      if (this.sentence == "") {
        return "";
      }
      return "py" + btoa(unescape(encodeURIComponent(str)));
    },
    hukugou: function (str) {
      //   str = str.slice(2);
      //   return decodeURIComponent(escape(atob(str)));
      let res = "";
      str = str.split(/\n/);
      console.log("str:", str);
      str.forEach((one_str) => {
        one_str = one_str.slice(2);
        console.log(one_str);
        res +=
          decodeURIComponent(escape(atob(one_str))).replace(/\r?\n/g, "") +
          "\n";
      });
      console.log("res:", res);

      return res;
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
    sentence_show: function () {
      return this.angouka(this.angouka(this.sentence));
    },

    hukugou_str: function () {
      //   let res = "";
      //   this.angou = this.angou.split(/\n/);
      //   console.log(this.angou);
      //   this.angou.forEach((one_str) => {
      //     one_str = one_str.slice(2);
      //     console.log(one_str);
      //     res += decodeURIComponent(escape(atob(one_str)));
      //   });
      //   console.log(res);

      //   return res;
      return this.hukugou(this.hukugou(this.angou));
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

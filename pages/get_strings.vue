<template>
  <div class="box">
    <div style="margin: 50px 40% 50px; font-size: 80%">
      <textarea id="copyTarget" type="text" :value="sentence_show" readonly />
      <button @click="copyToClipboard()">コピー</button>
    </div>
    <!-- <div style="margin: 0px 40% 30px; font-size: 80%">
      {{ hukugou_str }}
    </div> -->
    <div>
      <input type="file" @change="loadCsvFile" />
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      sentence: "",
      count: 0,
    };
  },
  methods: {
    copyToClipboard: function (target_str) {
      // コピー対象をJavaScript上で変数として定義する
      var clipboardText = target_str;
      navigator.clipboard.writeText(clipboardText);
    },
  },
  computed: {
    sentence_show: function () {
      return this.angouka(this.angouka(this.sentence));
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

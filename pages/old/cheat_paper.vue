<template>
  <div class="box">
    <div
      :style="{
        height: '120px',
        margin: '0px 40% 300px',
        fontSize: parseInt(fontSize) + '%',
      }"
    >
      {{ sentence_show }}
    </div>
    <textarea v-model="sentence" />
    <div>{{ count }}</div>
    <button @click="count = 0">リセット</button>
    <button @click="count++">次へ</button>
    <br />
    <input v-model="fontSize" />
  </div>
</template>
<script>
export default {
  data() {
    return {
      sentence: "",
      count: 0,
      fontSize: 80,
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

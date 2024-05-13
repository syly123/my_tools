<template>
  <div>
    {{ words_not_in_list }}
    <textarea v-model="sentence" />
  </div>
</template>
<script>
import registered_list from "/Users/User/Downloads/registered_list.js";
export default {
  data() {
    return {
      sentence: "",
      registered_list: registered_list,
    };
  },
  methods: {},
  computed: {
    words_list: function () {
      console.log(this.sentence);
      return this.sentence.split(/ |,|\.|\n/g); //|,|.|:
    },
    words_not_in_list: function () {
      console.log(this.words_list);
      let words = [];
      for (let i = 0; i < this.words_list.length; i++) {
        const word = this.words_list[i];
        if (!registered_list.registered_list.includes(word.toLowerCase())) {
          words.push(word);
        }
      }
      console.log(words);
      return words;
    },
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

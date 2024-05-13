<template>
  <center>
    <div>
      <input type="file" @change="loadCsvFile" />
    </div>
  </center>
</template>
<script>
export default {
  data() {
    return {
      level: 0,
    };
  },
  methods: {
    //""内のカンマを分割しないためのコード
    csvSplit(line) {
      var c = "";
      var s = new String();
      var data = new Array();
      var singleQuoteFlg = false;

      for (var i = 0; i < line.length; i++) {
        c = line.charAt(i);
        if (c == "," && !singleQuoteFlg) {
          data.push(s.toString());
          s = "";
        } else if (c == "," && singleQuoteFlg) {
          s = s + c;
        } else if (c == '"') {
          singleQuoteFlg = !singleQuoteFlg;
        } else {
          s = s + c;
        }
      }
      return data;
    },
    loadCsvFile(e) {
      let vm = this;
      vm.workers = [];
      vm.message = "";
      let file = e.target.files[0];

      //CSVファイルかどうかを判別
      if (!file.type.match("text/csv")) {
        vm.message = "CSVファイルを選択してください";
        return;
      }

      let reader = new FileReader();
      reader.readAsText(file);

      reader.onload = () => {
        let lines = reader.result.split("\n");
        let header = lines[0].split(",");
        vm.tweetIndex = header.indexOf("tweet");
        vm.likesCountIndex = header.indexOf("likes_count");
        vm.retweetsCountIndex = header.indexOf("retweets_count");
        vm.photoIndex = header.indexOf("photos");
        vm.linkIndex = header.indexOf("link");
        vm.jaIndex = header.indexOf("和訳");
        lines.shift(); //csvのheader削除
        console.log(header);
        let linesArr = [];
        for (let i = 0; i < lines.length; i++) {
          linesArr[i] = this.csvSplit(lines[i]);
        }
        vm.workers = linesArr;
      };
    },
    moveLink: function (url) {
      window.open(url, "_blank");
    },
    scrollTop: function () {
      window.scrollTo({
        top: 0,
        behavior: "instant",
      });
    },
    changePage: function (direction) {
      if (direction == "right") {
        this.page++;
      } else {
        this.page--;
      }
      this.scrollTop();
    },
    keyCheck: function (event) {
      if (event.defaultPrevented) {
        return;
      }
      switch (event.key) {
        case "ArrowLeft":
          this.changePage("left");
          break;
        case "ArrowRight":
          this.changePage("right");
          break;
        default:
          return;
      }
      event.preventDefault();
    },
  },
  computed: {
    tweets_should_load: function () {
      let len = 586;
      return this.workers
        .slice(
          len - (this.page + 1) * this.ShowInOneTime,
          len - this.page * this.ShowInOneTime
        )
        .reverse();
    },
  },
  created: function () {
    window.addEventListener("keydown", this.keyCheck);
  },

  beforeDestroy() {
    window.removeEventListener("keydown", this.keyCheck);
  },
};
</script>

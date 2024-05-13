<template>
  <center>
    <div>
      <input type="file" @change="loadCsvFile" />
      <div>
        <button @click="page--">ー</button>
        {{ page }}
        <button @click="page++">＋</button>
      </div>

      <table border="1">
        <div
          v-for="(worker, index) in tweets_should_load"
          :key="index"
          style="margin-bottom: 30px"
        >
          <div @click="moveLink(worker[linkIndex])">
            {{
              ShowInOneTime * page + index
            }}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;🔁{{
              Number(worker[retweetsCountIndex]).toLocaleString()
            }}
            ♥{{ Number(worker[likesCountIndex]).toLocaleString() }} ____
            {{ worker[timeIndex] }}
          </div>
          <textarea
            style="width: 600px; height: 50px"
            :value="worker[tweetIndex]"
            onfocus="this.select();"
          />
          <br />
          <textarea
            @dblclick="
              moveLink(
                `https://twitter.com/search?q=conversation_id%3A${worker[
                  linkIndex
                ]
                  .split('/')
                  .pop()}%20include%3Areplies%20include%3Aquote%20lang%3Aja&src=typed_query`
              )
            "
            :value="worker[jaIndex]"
            style="width: 600px; height: 35px"
          ></textarea>

          <div>
            <!-- <a :href="worker[photoIndex]" target="_blank">画像</a> -->
            <img
              @click="moveLink(worker[photoIndex])"
              v-if="worker[photoIndex] != ''"
              :src="worker[photoIndex]"
            />
          </div>
        </div>
      </table>
      <div>
        <button @click="page--">ー</button>
        current page :{{ page }}
        <button
          @click="
            page++;
            scrollTop();
          "
        >
          ＋
        </button>
      </div>
    </div>
    <p>ツイート番号の段をクリックでツイートページに飛ぶ</p>
    <p>和訳欄をダブルクリックで日本語のみのリプライ一覧に飛ぶ</p>
  </center>
</template>
<script>
export default {
  data() {
    return {
      message: "",
      workers: [],
      page: 0,
      tweetIndex: 0,
      photoIndex: 0,
      linkIndex: 0,
      likesCountIndex: 0,
      retweetsCountIndex: 0,
      timeIndex: 0,
      jaIndex: 0,
      ShowInOneTime: 20,
      viewType: "normal",
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
        vm.timeIndex = header.indexOf("JST");
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
    tweets_should_load_reversed: function () {
      let len = 586;
      return this.workers
        .slice(
          len - (this.page + 1) * this.ShowInOneTime,
          len - this.page * this.ShowInOneTime
        )
        .reverse();
    },
    tweets_should_load_nomal: function () {
      return this.workers.slice(
        this.page * this.ShowInOneTime,
        (this.page + 1) * this.ShowInOneTime
      );
    },
    tweets_should_load: function () {
      return this.viewType == "normal"
        ? this.tweets_should_load_nomal
        : this.tweets_should_load_reversed;
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

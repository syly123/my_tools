<template>
  <!-- attack_calculator -->
  <div>
    <h2>デカアクア深夜浮上日計算機</h2>
    <p>島の表記</p>
    <input v-model="island_name" />
    <h3>現在の占領完了までの残り時間</h3>
    <input v-model.number="hours" />時間<input v-model.number="minutes" />分
    <!-- <p>つまり、残り{{ remaining_time }}分 m = {{ minutes }}</p> -->
    <h3>再浮上時刻一覧</h3>
    <p v-for="(rt, i) in reappearance_times" :key="i">
      <button @click="copyRemindSentence(rt)">copy</button>
      {{ rt }}
    </p>
    <textarea id="textarea" v-model="copySent"></textarea>
  </div>
</template>

<script>
export default {
  data() {
    return {
      hours: 0,
      minutes: 0,
      copySent: "リマインダーに貼り付ける文",
      island_name: "",
    };
  },
  methods: {
    copyRemindSentence: function (rt) {
      let sentence = "";
      let hour =
        rt.substr(14, 2) > 12
          ? "午後" + (rt.substr(14, 2) - 12)
          : "午前" + rt.substr(14, 2);
      sentence =
        rt.substr(0, 2) +
        "日の" +
        hour +
        "時" +
        rt.substr(17, 2) +
        "分の20分前に" +
        this.island_name +
        "〜20とリマインド";
      this.copySent = sentence;
      navigator.clipboard.writeText(this.copySent);
      //   let textarea = document.getElementById("textarea");
      //   textarea.select();
      //   document.execCommand("copy");
    },
  },
  computed: {
    remaining_time: function () {
      console.log("rt has changed");
      return this.hours * 60 + this.minutes;
    },
    reappearance_times: function () {
      let reappearance_times = [];
      let dt = new Date();
      dt.setMinutes(dt.getMinutes() + (this.remaining_time + 4320));
      let dt_str = "";
      for (let i = 0; i < 9; i++) {
        let dayOfWeek = dt.getDay();
        let dayOfWeekStr = ["日", "月", "火", "水", "木", "金", "土"][
          dayOfWeek
        ];
        dt_str = dt.toString();
        dt_str =
          dt_str.substr(8, 2) +
          "(" +
          dayOfWeekStr +
          ")" +
          "    の    " +
          dt_str.substr(16, 5);
        reappearance_times.push(dt_str);
        dt.setMinutes(dt.getMinutes() + 5400 + 10);
        console.log(dt);
      }
      return reappearance_times;
    },
  },
};
</script>

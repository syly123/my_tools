<template>
  <!-- attack_calculator -->
  <div>
    <h2>tatsuhikoのエンパ計算機だよ</h2>
    <h1>攻撃側</h1>
    <p v-for="soldier in attack_soldiers" :key="soldier.power[3]">
      {{ soldier.name }}→<input v-model="soldier.num[0]" />体
    </p>

    <h4>
      乱闘総攻撃力: {{ rantou_attack_total_power }} 遠隔総攻撃力:
      {{ enkaku_attack_total_power }}
    </h4>

    <h1>防御側</h1>
    <p v-for="soldier in defense_soldiers" :key="soldier.power[3]">
      {{ soldier.name }}→<input v-model="soldier.num[1]" />体
    </p>

    <h4>
      乱闘総防御力: {{ rantou_defense_total_power }} 遠隔総防御力:
      {{ enkaku_defense_total_power }}
    </h4>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "攻撃側",
      soldiers: {
        //乱攻撃、遠攻撃、乱防御、遠防御の順power
        beteran_sinokyouhu: {
          name: "ベテラン死の恐怖",
          num: [0, 0],
          power: [0, 175, 17, 26],
        },
        beteran_akumanokyouhu: {
          name: "ベテラン悪魔の恐怖",
          num: [0, 0],
          power: [200, 0, 21, 6],
        },
        akumanokyouhu: {
          name: "悪魔の恐怖",
          num: [0, 0],
          power: [185, 0, 19, 5],
        },
        sinokyouhu: { name: "死の恐怖", num: [0, 0], power: [0, 162, 15, 24] },
        teikokuyumihei: {
          name: "帝国弓兵",
          num: [0, 0],
          power: [0, 124, 17, 26],
        },
        konoehei: { name: "近衛兵", num: [0, 0], power: [135, 0, 21, 8] },
        teikokunosyasyu: {
          name: "帝国の射手",
          num: [0, 0],
          power: [0, 132, 17, 26],
        },
        teikokukisi: { name: "帝国騎士", num: [0, 0], power: [143, 0, 21, 8] },
        seieibutainoyumihei: {
          name: "精鋭部隊の弓兵",
          num: [0, 0],
          power: [0, 129, 14, 23],
        },
        seieibutainokisi: {
          name: "精鋭部隊の騎士",
          num: [0, 0],
          power: [140, 0, 18, 5],
        },
        ounogoeitai_banhei: {
          name: "王の護衛隊・番兵",
          num: [0, 0],
          power: [14, 0, 150, 65],
        },
        ounogoeitai_teisatuhei: {
          name: "王の護衛隊・偵察兵",
          num: [0, 0],
          power: [0, 16, 70, 139],
        },
        syasyu: { name: "射手", num: [0, 0], power: [0, 10, 53, 55] },
        onoyarihei: { name: "斧やり兵", num: [0, 0], power: [17, 0, 135, 50] },
        nagayumihei: { name: "長弓兵", num: [0, 0], power: [0, 20, 56, 125] },
        jukuren_onoyarihei: {
          name: "熟練斧やり兵",
          num: [0, 0],
          power: [15, 0, 145, 60],
        },
        jukuren_nagayumihei: {
          name: "熟練長弓兵",
          num: [0, 0],
          power: [0, 17, 67, 134],
        },
        jukuren_yarihei: {
          name: "熟練槍兵",
          num: [0, 0],
          power: [15, 0, 142, 57],
        },
        jukuren_yumihei: {
          name: "熟練弓兵",
          num: [0, 0],
          power: [0, 18, 65, 132],
        },
        beteran_hukugouyumi: {
          name: "ベテラン複合弓",
          num: [0, 0],
          power: [0, 20, 84, 183],
        },
        beteran_kaenhousyahei: {
          name: "ベテラン火炎放射兵",
          num: [0, 0],
          power: [18, 0, 196, 79],
        },
        hukugouyumisyasyu: {
          name: "複合弓射手",
          num: [0, 0],
          power: [0, 16, 74, 159],
        },
        kaenhousyahei: {
          name: "火炎放射兵",
          num: [0, 0],
          power: [14, 0, 170, 68],
        },
      },
      beteran_sinokyouhu: { num: [0, 0], power: 175 },
      beteran_akumanokyouhu: { num: [0, 0], power: 200 },
      sinokyouhu: { num: [0, 0], power: [] },
    };
  },
  computed: {
    rantou_attack_total_power: function () {
      let rantou_attack_total_power = 0;
      for (let key in this.soldiers) {
        rantou_attack_total_power +=
          this.soldiers[key].num[0] * this.soldiers[key].power[0];
      }
      return rantou_attack_total_power;
    },
    enkaku_attack_total_power: function () {
      let enkaku_attack_total_power = 0;
      for (let key in this.soldiers) {
        enkaku_attack_total_power +=
          this.soldiers[key].num[0] * this.soldiers[key].power[1];
      }
      return enkaku_attack_total_power;
    },
    rantou_defense_total_power: function () {
      let rantou_defense_total_power = 0;
      for (let key in this.soldiers) {
        rantou_defense_total_power +=
          this.soldiers[key].num[1] * this.soldiers[key].power[2];
      }
      return rantou_defense_total_power;
    },
    enkaku_defense_total_power: function () {
      let enkaku_defense_total_power = 0;
      for (let key in this.soldiers) {
        enkaku_defense_total_power +=
          this.soldiers[key].num[1] * this.soldiers[key].power[3];
      }
      return enkaku_defense_total_power;
    },
    attack_soldiers: function () {
      let attack_soldiers = {};
      for (let key in this.soldiers) {
        if (this.soldiers[key].power[0] + this.soldiers[key].power[1] > 50) {
          attack_soldiers[key] = this.soldiers[key];
        }
      }
      return attack_soldiers;
    },
    defense_soldiers: function () {
      let defense_soldiers = {};
      for (let key in this.soldiers) {
        if (this.soldiers[key].power[2] + this.soldiers[key].power[3] > 100) {
          defense_soldiers[key] = this.soldiers[key];
        }
      }
      return defense_soldiers;
    },
  },
};
</script>

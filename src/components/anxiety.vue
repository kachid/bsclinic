<template>
  <Dialog
    nameOfTest="Оценка уровня тревожности"
    header="Шкала самооценки тревоги"
    subquestion="насколько данное состояние выражено именно СЕЙЧАС, В ДАННЫЙ МОМЕНТ, СЕГОДНЯ"
    :maxPage="maxPage"
    :questions="questions"
    :sliders="sliders"
    :isAlarm="isAlarm"
  />
</template>
<script>
import Dialog from "@/components/dialog";

export default {
  components: {
    Dialog
  },
  data: () => ({
    questions: que,
    results: outcome,
    rules: [v => v < 7 || "высокий уровень"],
    rulesForSumAll: [v => v <= 20 || ""]
  }),
  computed: {
    maxPage() {
      return this.questions.length;
    },
    sumA() {
      const sumOfRatio = this.sumOfPointsRatio("A");
      switch (true) {
        case sumOfRatio <= 26:
          return 1;
        case sumOfRatio > 26 && sumOfRatio < 37:
          return 2;
        case sumOfRatio > 36 && sumOfRatio < 48:
          return 3;
        case sumOfRatio > 47 && sumOfRatio < 58:
          return 4;
        case sumOfRatio > 57 && sumOfRatio < 83:
          return 5;
        case sumOfRatio > 82 && sumOfRatio < 123:
          return 6;
        case sumOfRatio > 122 && sumOfRatio < 162:
          return 7;
        case sumOfRatio > 161 && sumOfRatio < 202:
          return 8;
        case sumOfRatio > 201:
          return 9;
        default:
          if (typeof sumOfRatio === "undefined") {
            console.error("Error in sumA", sumOfRatio);
          }
          break;
      }
    },
    sumO() {
      const sumOfRatio = this.sumOfPointsRatio("O");
      switch (true) {
        case sumOfRatio <= 44:
          return 1;
        case sumOfRatio > 44 && sumOfRatio < 63:
          return 2;
        case sumOfRatio > 62 && sumOfRatio < 81:
          return 3;
        case sumOfRatio > 80 && sumOfRatio < 98:
          return 4;
        case sumOfRatio > 97 && sumOfRatio < 123:
          return 5;
        case sumOfRatio > 122 && sumOfRatio < 156:
          return 6;
        case sumOfRatio > 155 && sumOfRatio < 188:
          return 7;
        case sumOfRatio > 187 && sumOfRatio < 220:
          return 8;
        case sumOfRatio > 219:
          return 9;
        default:
          if (typeof sumOfRatio === "undefined") {
            console.error("Error in sumO", sumOfRatio);
          }
          break;
      }
    },
    sumE() {
      const sumOfRatio = this.sumOfPointsRatio("E");
      switch (true) {
        case sumOfRatio <= 34:
          return 1;
        case sumOfRatio > 34 && sumOfRatio < 49:
          return 2;
        case sumOfRatio > 48 && sumOfRatio < 63:
          return 3;
        case sumOfRatio > 62 && sumOfRatio < 77:
          return 4;
        case sumOfRatio > 76 && sumOfRatio < 101:
          return 5;
        case sumOfRatio > 100 && sumOfRatio < 138:
          return 6;
        case sumOfRatio > 137 && sumOfRatio < 174:
          return 7;
        case sumOfRatio > 173 && sumOfRatio < 210:
          return 8;
        case sumOfRatio > 209:
          return 9;
        default:
          if (typeof sumOfRatio === "undefined") {
            console.error("Error in sumE", sumOfRatio);
          }
          break;
      }
    },
    sumF() {
      const sumOfRatio = this.sumOfPointsRatio("F");
      switch (true) {
        case sumOfRatio <= 13:
          return 1;
        case sumOfRatio > 13 && sumOfRatio < 20:
          return 2;
        case sumOfRatio > 19 && sumOfRatio < 25:
          return 3;
        case sumOfRatio > 24 && sumOfRatio < 30:
          return 4;
        case sumOfRatio > 29 && sumOfRatio < 55:
          return 5;
        case sumOfRatio > 54 && sumOfRatio < 100:
          return 6;
        case sumOfRatio > 99 && sumOfRatio < 145:
          return 7;
        case sumOfRatio > 144 && sumOfRatio < 189:
          return 8;
        case sumOfRatio > 188:
          return 9;
        default:
          if (typeof sumOfRatio === "undefined") {
            console.error("Error in sumF", sumOfRatio);
          }
          break;
      }
    },
    sumC() {
      const sumOfRatio = this.sumOfPointsRatio("C");
      switch (true) {
        case sumOfRatio <= 50:
          return 1;
        case sumOfRatio > 50 && sumOfRatio < 71:
          return 2;
        case sumOfRatio > 70 && sumOfRatio < 91:
          return 3;
        case sumOfRatio > 90 && sumOfRatio < 111:
          return 4;
        case sumOfRatio > 110 && sumOfRatio < 136:
          return 5;
        case sumOfRatio > 135 && sumOfRatio < 166:
          return 6;
        case sumOfRatio > 165 && sumOfRatio < 196:
          return 7;
        case sumOfRatio > 195 && sumOfRatio < 226:
          return 8;
        case sumOfRatio > 225:
          return 9;
        default:
          if (typeof sumOfRatio === "undefined") {
            console.error("Error in sumC", sumOfRatio);
          }
          break;
      }
    },
    sumAll() {
      return this.sumOfPointsAmount();
    },
    resultA() {
      return this.countResult(this.sumA, "Астенический компонент тревожности");
    },
    resultO() {
      return this.countResult(this.sumO, "Тревожная оценка перспективы");
    },
    resultE() {
      return this.countResult(this.sumE, "Эмоциональный дискомфорт");
    },
    resultF() {
      return this.countResult(this.sumF, "Фобический компонент");
    },
    resultC() {
      return this.countResult(this.sumC, "Социальная защита");
    },
    resultAll() {
      return this.countResult(this.sumAll, "общей тревоги");
    },
    isAlarm() {
      return (
        this.sumA > 6 ||
        this.sumF > 6 ||
        this.sumG > 6 ||
        this.sumM > 6 ||
        this.sumP > 6 ||
        this.sumAll > 19
      );
    },
    sliders() {
      return [
        {
          min: "1",
          max: "45",
          label: "Общая сумма баллов",
          value: this.sumAll,
          rules: this.rulesForSumAll,
          result: this.resultAll
        },
        {
          min: "1",
          max: "9",
          label: "Астенический компонент тревожности",
          value: this.sumA,
          rules: this.rules
        },
        {
          min: "1",
          max: "9",
          label: "Тревожная оценка перспективы",
          value: this.sumO,
          rules: this.rules
        },
        {
          min: "1",
          max: "9",
          label: "Эмоциональный дискомфорт",
          value: this.sumE,
          rules: this.rules
        },
        {
          min: "1",
          max: "9",
          label: "Фобический компонент",
          value: this.sumF,
          rules: this.rules
        },
        {
          min: "1",
          max: "9",
          label: "Социальная защита",
          value: this.sumC,
          rules: this.rules
        }
      ];
    }
  },
  methods: {
    sumOfPointsRatio(key) {
      let arr = [...this.questions].filter(item => item.type === key);

      return arr.reduce((sum, curr) => sum + curr.choice.ratio, 0);
    },
    sumOfPointsAmount() {
      let arr = [...this.questions];

      return arr.reduce((sum, curr) => sum + curr.choice.amount, 0);
    },
    countResult(points, key) {
      let result;

      if (points < 12) {
        result = this.results[0];
      } else if (points > 19) {
        result = this.results[2] + key;
      } else {
        result = this.results[1] + key;
      }
      return result;
    }
  }
};
const answ = ["совсем нет", "слабо выражено", "выражено", "очень выражено"],
  que = [
    {
      id: 1,
      type: "E",
      q: "Я нахожусь в напряжении",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 25 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 49 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 74 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 2,
      type: "E",
      q: "Я расстроен",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 24 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 49 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 73 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 3,
      type: "O",
      q: "Я тревожусь о будущем",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 37 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 74 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 110 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 4,
      type: "E",
      q: "Я нервничаю",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 27 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 53 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 80 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 5,
      type: "O",
      q: "Я озабочен",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 32 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 65 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 98 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 6,
      type: "E",
      q: "Я возбужден",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 24 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 49 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 73 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 7,
      type: "F",
      q: "Я ощущаю непонятную угрозу",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 37 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 74 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 111 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 8,
      type: "A",
      q: "Я быстро устаю",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 30 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 61 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 91 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 9,
      type: "F",
      q: "Я не уверен в себе",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 28 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 56 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 85 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 10,
      type: "C",
      q: "Я избегаю любых конфликтов",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 57 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 114 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 171 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 11,
      type: "C",
      q: "Я легко прихожу в замешательство",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 43 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 86 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 129 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 12,
      type: "F",
      q: "Я ощущаю свою бесполезность",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 29 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 58 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 87 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 13,
      type: "A",
      q: "Я плохо сплю",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 41 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 81 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 122 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 14,
      type: "A",
      q: "Я ощущаю себя утомленным",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 29 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 58 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 87 } }
      ],
      choice: { amount: null, ratio: null }
    },
    {
      id: 15,
      type: "O",
      q: "Я эмоционально чувствителен",
      a: [
        { id: 0, answer: answ[0], points: { amount: 0, ratio: 0 } },
        { id: 1, answer: answ[1], points: { amount: 1, ratio: 31 } },
        { id: 2, answer: answ[2], points: { amount: 2, ratio: 61 } },
        { id: 3, answer: answ[3], points: { amount: 3, ratio: 92 } }
      ],
      choice: { amount: null, ratio: null }
    }
  ],
  outcome = ["Низкий уровень ", "Нормальный уровень ", "Высокий уровень "];
</script>

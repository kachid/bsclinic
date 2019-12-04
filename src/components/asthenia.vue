<template>
  <Dialog
    nameOfTest="Тестирование уровня астении"
    header="Субъективная шкала оценки астении"
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
    rules: [v => v <= 12 || "Астенический синдром"],
    rulesForSumAll: [v => v <= 20 || ""]
  }),
  computed: {
    maxPage() {
      return this.questions.length;
    },
    sumA() {
      return this.sumOfPoints("A");
    },
    sumF() {
      return this.sumOfPoints("F");
    },
    sumG() {
      return this.sumOfPoints("G");
    },
    sumM() {
      return this.sumOfPoints("M");
    },
    sumP() {
      return this.sumOfPoints("P");
    },
    sumAll() {
      return this.sumA + this.sumF + this.sumG + this.sumM + this.sumP;
    },
    resultA() {
      return this.countResult(this.sumA, "Пониженная активность");
    },
    resultF() {
      return this.countResult(this.sumF, "Физическая астения");
    },
    resultG() {
      return this.countResult(this.sumG, "Общая астения ");
    },
    resultM() {
      return this.countResult(this.sumM, "Снижение мотивации");
    },
    resultP() {
      return this.countResult(this.sumP, "Психическая астения");
    },
    resultAll() {
      return this.countResult(this.sumAll, "астенический синдром");
    },
    isAlarm() {
      return (
        this.sumA > 12 ||
        this.sumF > 12 ||
        this.sumG > 12 ||
        this.sumM > 12 ||
        this.sumP > 12 ||
        this.sumAll > 20
      );
    },
    sliders() {
      return [
        {
          min: "0",
          max: "100",
          label: "Общая сумма баллов",
          value: this.sumAll,
          rules: this.rulesForSumAll,
          result: this.resultAll
        },
        {
          min: "0",
          max: "20",
          label: "Пониженная активность",
          value: this.sumA,
          rules: this.rules
        },
        {
          min: "0",
          max: "20",
          label: "Физическая астения",
          value: this.sumF,
          rules: this.rules
        },
        {
          min: "0",
          max: "20",
          label: "Общая астения",
          value: this.sumG,
          rules: this.rules
        },
        {
          min: "0",
          max: "20",
          label: "Снижение мотивации",
          value: this.sumM,
          rules: this.rules
        },
        {
          min: "0",
          max: "20",
          label: "Психическая астения",
          value: this.sumP,
          rules: this.rules
        }
      ];
    }
  },
  methods: {
    sumOfPoints(key) {
      let arr = [...this.questions].filter(item => item.type === key);

      return arr.reduce((sum, curr) => sum + curr.choice, 0);
    },
    countResult(points, key) {
      let result;

      if (points < 20) {
        result = this.results[0];
      } else if (points > 30) {
        result = this.results[2] + key;
      } else {
        result = this.results[1] + key;
      }
      return result;
    }
  }
};
const answ = [
    "да, это правда",
    "чаще это правда",
    "иногда это правда",
    "редко это правда",
    "нет, это неправда"
  ],
  que = [
    {
      id: 1,
      type: "G",
      q: "Я чувствую себя здоровым",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 2,
      type: "F",
      q: "Физически я способен на немногое",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 3,
      type: "A",
      q: "Я чувствую себя активным",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 4,
      type: "M",
      q: "Всё, что я делаю, доставляет мне удовольствие",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 5,
      type: "G",
      q: "Я чувствую себя усталым",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 6,
      type: "A",
      q: "Мне кажется, я многое успеваю за день",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 7,
      type: "P",
      q: "Когда я занимаюсь чем-либо, я могу сконцентрироваться на этом",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 8,
      type: "F",
      q: "Физически я способен на многое",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 9,
      type: "M",
      q: "Я боюсь дел, которые мне необходимо сделать",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 10,
      type: "A",
      q: "Я думаю, что за день выполняю очень мало дел",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 11,
      type: "P",
      q: "Я могу хорошо концентрировать внимание",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 12,
      type: "G",
      q: "Я чувствую себя отдохнувшим",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 13,
      type: "P",
      q: "Мне требуется много усилий для концентрации внимания",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 14,
      type: "F",
      q: "Физически я чувствую себя в плохом состоянии",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 15,
      type: "M",
      q: "У меня много планов",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    },
    {
      id: 16,
      type: "G",
      q: "Я быстро устаю",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 17,
      type: "A",
      q: "Я очень мало успеваю сделать",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 18,
      type: "M",
      q: "Мне кажется, что я ничего не делаю",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 19,
      type: "P",
      q: "Мои мысли легко рассеиваются",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[3], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[1], points: 2 },
        { id: 4, answer: answ[0], points: 1 }
      ],
      choice: null
    },
    {
      id: 20,
      type: "F",
      q: "Физически я чувствую себя в прекрасном состоянии",
      a: [
        { id: 0, answer: answ[0], points: 1 },
        { id: 1, answer: answ[1], points: 2 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 4 },
        { id: 4, answer: answ[4], points: 5 }
      ],
      choice: null
    }
  ],
  outcome = [
    "Норма, отсутствие достоверно выраженных симптомов",
    "Субклинически выраженный ",
    "Клинически выраженный "
  ];
</script>

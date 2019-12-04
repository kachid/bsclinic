<template>
  <Dialog
    nameOfTest="Анкета оценки качества сна"
    header="Субъективная шкала оценки сна Шпигеля"
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
    rulesForSumAll: [
      v =>
        v > 22
          ? true
          : v < 18
          ? "сон значительно нарушен"
          : "лёгкие нарушения сна"
    ]
  }),
  computed: {
    maxPage() {
      return this.questions.length;
    },
    sumA() {
      return this.sumOfPoints("A");
    },
    sumAll() {
      return this.sumA;
    },
    resultAll() {
      return this.countResult(this.sumAll, "");
    },
    isAlarm() {
      return this.sumAll < 18;
    },
    sliders() {
      return [
        {
          min: "7",
          max: "30",
          label: "Общая сумма баллов",
          value: this.sumAll,
          rules: this.rulesForSumAll,
          result: this.resultAll
        }
      ]
    }
  },
  methods: {
    sumOfPoints(key) {
      let arr = [...this.questions].filter(item => item.type === key);

      return arr.reduce((sum, curr) => sum + curr.choice, 0);
    },
    countResult(points, key) {
      let result;

      if (points > 22) {
        result = this.results[0];
      } else if (points < 18) {
        result = this.results[2] + key;
      } else {
        result = this.results[1] + key;
      }
      return result;
    }
  }
};
const answ = [
    "мгновенно",
    "недолго (5-15 мин)",
    "средне (15-30 мин)",
    "долго (более 30 мин)",
    "очень долгий сон (более 10 часов)",
    "долгий (8-10 часов)",
    "средний (6-8 часов)",
    "короткий (4-6 часов)",
    "очень короткий (менее 4 часов)",
    "нет",
    "редко (1 за ночь)",
    "не часто (2-3 за ночь)",
    "часто (более 4 за ночь)",
    "очень часто (более 10 за ночь)",
    "отлично",
    "хорошо",
    "средне",
    "плохо",
    "очень плохо",
    "временами",
    "умеренно",
    "множественные",
    "множественные и тревожные"
  ],
  que = [
    {
      id: 1,
      type: "A",
      q: "Время засыпания:",
      a: [
        { id: 0, answer: answ[0], points: 5 },
        { id: 1, answer: answ[1], points: 4 },
        { id: 2, answer: answ[2], points: 3 },
        { id: 3, answer: answ[3], points: 2 }
      ],
      choice: null
    },
    {
      id: 2,
      type: "A",
      q: "Продолжительность сна:",
      a: [
        { id: 0, answer: answ[4], points: 5 },
        { id: 1, answer: answ[5], points: 4 },
        { id: 2, answer: answ[6], points: 3 },
        { id: 3, answer: answ[7], points: 2 },
        { id: 4, answer: answ[8], points: 1 }
      ],
      choice: null
    },
    {
      id: 3,
      type: "A",
      q: "Количество ночных пробуждений:",
      a: [
        { id: 0, answer: answ[9], points: 5 },
        { id: 1, answer: answ[10], points: 4 },
        { id: 2, answer: answ[11], points: 3 },
        { id: 3, answer: answ[12], points: 2 },
        { id: 4, answer: answ[13], points: 1 }
      ],
      choice: null
    },
    {
      id: 4,
      type: "A",
      q: "Качество сна",
      a: [
        { id: 0, answer: answ[14], points: 5 },
        { id: 1, answer: answ[15], points: 4 },
        { id: 2, answer: answ[16], points: 3 },
        { id: 3, answer: answ[17], points: 2 },
        { id: 4, answer: answ[18], points: 1 }
      ],
      choice: null
    },
    {
      id: 5,
      type: "A",
      q: "Количество сновидений:",
      a: [
        { id: 0, answer: answ[9], points: 5 },
        { id: 1, answer: answ[19], points: 4 },
        { id: 2, answer: answ[20], points: 3 },
        { id: 3, answer: answ[21], points: 2 },
        { id: 4, answer: answ[22], points: 1 }
      ],
      choice: null
    },
    {
      id: 6,
      type: "A",
      q: "Качество утреннего пробуждения:",
      a: [
        { id: 0, answer: answ[14], points: 5 },
        { id: 1, answer: answ[15], points: 4 },
        { id: 2, answer: answ[16], points: 3 },
        { id: 3, answer: answ[17], points: 2 },
        { id: 4, answer: answ[18], points: 1 }
      ],
      choice: null
    }
  ],
  outcome = [
    "Сон не нарушен",
    "Лёгкие нарушения сна",
    "Сон значительно нарушен"
  ];
</script>

<template>
  <Dialog
    nameOfTest="Тестирование уровня депрессии"
    header="Госпитальная шкала тревоги и депрессии"
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
    rules: [v => v <= 7 || ""]
  }),
  computed: {
    maxPage() {
      return this.questions.length;
    },
    sumT() {
      return this.sumOfPoints("T");
    },
    sumD() {
      return this.sumOfPoints("D");
    },
    resultT() {
      return this.countResult(this.sumT, "тревога");
    },
    resultD() {
      return this.countResult(this.sumD, "депрессия");
    },
    isAlarm() {
      return this.sumT > 7 || this.sumD > 7;
    },
    sliders() {
      return [
        {
          min: "0",
          max: "21",
          label: "Тревога",
          value: this.sumT,
          rules: this.rules,
          result: this.resultT
        },
        {
          min: "0",
          max: "21",
          label: "Депрессия",
          value: this.sumD,
          rules: this.rules,
          result: this.resultD
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

      if (points < 8) {
        result = this.results[0];
      } else if (points > 10) {
        result = this.results[2] + key;
      } else {
        result = this.results[1] + key;
      }
      return result;
    }
  }
};
const answ = [
    "всё время",
    "время от времени и не так часто",
    "время от времени, иногда",
    "большую часть времени",
    "определённо это так",
    "наверное, это так",
    "лишь в очень малой степени это так",
    "определённо это так, и страх очень сильный",
    "да, это так, но страх не очень сильный",
    "лишь изредка это так",
    "это совсем не так",
    "иногда, но это меня не беспокоит",
    "я не уделяю этому столько времени, сколько нужно",
    "может быть, я стал(а) меньше уделять этому внимания",
    "я слежу за собой также, как и раньше",
    "точно также, как и обычно",
    "да, но не в той степени, как раньше",
    "значительно меньше, чем обычно",
    "постоянно",
    "довольно часто",
    "не так уж часто",
    "часто",
    "очень часто",
    "редко",
    "очень редко",
    "иногда",
    "только иногда",
    "практически всё время",
    "совсем так не считаю",
    "совсем не бывает",
    "совсем не способен",
    "совсем не испытываю",
    "совсем не могу",
    "совсем нет"
  ],
  que = [
    {
      id: 1,
      type: "T",
      q: "Я испытываю напряженность, мне не по себе",
      a: [
        { id: 0, answer: answ[0], points: 3 },
        { id: 1, answer: answ[21], points: 2 },
        { id: 2, answer: answ[2], points: 1 },
        { id: 3, answer: answ[31], points: 0 }
      ],
      choice: null
    },
    {
      id: 2,
      type: "D",
      q:
        "То, что приносило мне большое удовольствие, и сейчас вызывает у меня такое же чувство",
      a: [
        { id: 0, answer: answ[4], points: 0 },
        { id: 1, answer: answ[5], points: 1 },
        { id: 2, answer: answ[6], points: 2 },
        { id: 3, answer: answ[10], points: 3 }
      ],
      choice: null
    },
    {
      id: 3,
      type: "T",
      q:
        "Я испытываю страх, кажется, будто что-то ужасное может вот-вот случиться",
      a: [
        { id: 0, answer: answ[7], points: 3 },
        { id: 1, answer: answ[8], points: 2 },
        { id: 2, answer: answ[11], points: 1 },
        { id: 3, answer: answ[31], points: 0 }
      ],
      choice: null
    },
    {
      id: 4,
      type: "D",
      q: "Я способен рассмеяться и увидеть в том или ином событии смешное",
      a: [
        { id: 0, answer: answ[4], points: 0 },
        { id: 1, answer: answ[5], points: 1 },
        { id: 2, answer: answ[6], points: 2 },
        { id: 3, answer: answ[30], points: 3 }
      ],
      choice: null
    },
    {
      id: 5,
      type: "T",
      q: "Беспокойные мысли крутятся у меня в голове",
      a: [
        { id: 0, answer: answ[18], points: 3 },
        { id: 1, answer: answ[3], points: 2 },
        { id: 2, answer: answ[1], points: 1 },
        { id: 3, answer: answ[26], points: 0 }
      ],
      choice: null
    },
    {
      id: 6,
      type: "D",
      q: "Я испытываю бодрость",
      a: [
        { id: 0, answer: answ[31], points: 3 },
        { id: 1, answer: answ[24], points: 2 },
        { id: 2, answer: answ[25], points: 1 },
        { id: 3, answer: answ[27], points: 0 }
      ],
      choice: null
    },
    {
      id: 7,
      type: "T",
      q: "Я легко могу сесть и расслабиться",
      a: [
        { id: 0, answer: answ[4], points: 0 },
        { id: 1, answer: answ[5], points: 1 },
        { id: 2, answer: answ[9], points: 2 },
        { id: 3, answer: answ[32], points: 3 }
      ],
      choice: null
    },
    {
      id: 8,
      type: "D",
      q: "Мне кажется, что я стал всё делать очень медленно",
      a: [
        { id: 0, answer: answ[27], points: 3 },
        { id: 1, answer: answ[21], points: 2 },
        { id: 2, answer: answ[25], points: 1 },
        { id: 3, answer: answ[33], points: 0 }
      ],
      choice: null
    },
    {
      id: 9,
      type: "T",
      q: "Я испытываю внутреннее напряжение или дрожь",
      a: [
        { id: 0, answer: answ[31], points: 0 },
        { id: 1, answer: answ[25], points: 1 },
        { id: 2, answer: answ[21], points: 2 },
        { id: 3, answer: answ[22], points: 3 }
      ],
      choice: null
    },
    {
      id: 10,
      type: "D",
      q: "Я не слежу за своей внешностью",
      a: [
        { id: 0, answer: answ[4], points: 3 },
        { id: 1, answer: answ[12], points: 2 },
        { id: 2, answer: answ[13], points: 1 },
        { id: 3, answer: answ[14], points: 0 }
      ],
      choice: null
    },
    {
      id: 11,
      type: "T",
      q: "Я испытываю неусидчивость, словно мне постоянно нужно двигаться",
      a: [
        { id: 0, answer: answ[4], points: 3 },
        { id: 1, answer: answ[5], points: 2 },
        { id: 2, answer: answ[6], points: 1 },
        { id: 3, answer: answ[31], points: 0 }
      ],
      choice: null
    },
    {
      id: 12,
      type: "D",
      q:
        "Я считаю, что мои дела (занятия, увлечения) могут принести мне чувство удовлетворения",
      a: [
        { id: 0, answer: answ[15], points: 0 },
        { id: 1, answer: answ[16], points: 1 },
        { id: 2, answer: answ[17], points: 2 },
        { id: 3, answer: answ[28], points: 3 }
      ],
      choice: null
    },
    {
      id: 13,
      type: "T",
      q: "У меня бывает внезапное чувство паники",
      a: [
        { id: 0, answer: answ[22], points: 3 },
        { id: 1, answer: answ[19], points: 2 },
        { id: 2, answer: answ[20], points: 1 },
        { id: 3, answer: answ[29], points: 0 }
      ],
      choice: null
    },
    {
      id: 14,
      type: "D",
      q:
        "Я могу получить удовольствие от хорошей книги, радио- или телепрограммы",
      a: [
        { id: 0, answer: answ[21], points: 0 },
        { id: 1, answer: answ[25], points: 1 },
        { id: 2, answer: answ[23], points: 2 },
        { id: 3, answer: answ[24], points: 3 }
      ],
      choice: null
    }
  ],
  outcome = [
    "Норма, отсутствие достоверно выраженных симптомов",
    "Субклинически выраженная ",
    "Клинически выраженная "
  ];
</script>

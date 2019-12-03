<template>
  <v-dialog v-model="dialog" persistent max-width="600" content-class="grey lighten-5">
    <template v-slot:activator="{ on }">
      <v-btn class="title" text small color="#1A9EA6" v-on="on">
        Тестирование уровня депрессии
      </v-btn>
    </template>
    <v-card class="elevation-6">
      <v-toolbar color="#1A9EA6" dark flat justify="space-between">
        <v-toolbar-title class="title font-weight-medium">
          Госпитальная шкала тревоги и депрессии
        </v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items>
          <v-btn text icon @click="close">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar-items>
      </v-toolbar>
      <v-card-text>
        <v-form v-if="page <= maxPage">
          <v-radio-group v-model="currentChoice" column>
            <p
              class="light-blue--text
                    text--darken-3
                    text-uppercase
                    font-weight-medium"
            >
              {{ questions[currentQuestion].q }}
            </p>
            <v-radio
              v-for="a in questions[currentQuestion].a"
              :key="a.id"
              :label="a.answer"
              class="title"
              color="orange darken-3"
              :value="a.id"
            ></v-radio>
          </v-radio-group>
        </v-form>
        <v-card v-else flat color="transparent">
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="21"
              label="Тревога"
              :value="sumT"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <p>{{ this.resultT }}</p>
          </v-card-text>
          <v-divider></v-divider>
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="21"
              label="Депрессия"
              :value="sumD"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <p>{{ this.resultD }}</p>
          </v-card-text>
          <v-divider v-if="isAlarm"></v-divider>
          <v-card-text v-if="isAlarm">
            <p>
              По результатам Вам рекомендовано обратиться к врачу-неврологу.
            </p>
            <p>
              Для записи к специалисту в нашей клинике позвоните по телефону
              <a href="tel:+79827371930">+7(982)737-19-30</a>
            </p>
          </v-card-text>
        </v-card>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn v-if="page < maxPage" @click="next" color="primary">
          дальше
        </v-btn>
        <v-btn v-if="page === maxPage" @click="beforeEnd" color="primary">
          результат
        </v-btn>
      </v-card-actions>
    </v-card>
    <div v-if="page <= maxPage" class="text-center mt-5">
      <v-pagination
        v-model="page"
        :length="maxPage"
        :total-visible="7"
      ></v-pagination>
    </div>
    <!-- SNACKBAR -->
    <v-snackbar
      class="text-center body-1"
      v-model="snackbar"
      :timeout="timeout"
      color="red"
    >
      Нужно выбрать вариант ответа
    </v-snackbar>
  </v-dialog>
</template>

<script>
export default {
  data: () => ({
    questions: que,
    choices: [],
    currentChoice: null,
    page: 1,
    results: outcome,
    rules: [v => v <= 7 || ""],
    snackbar: false,
    timeout: 2000,
    dialog: false
  }),
  computed: {
    currentQuestion() {
      return this.page - 1;
    },
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
    }
  },
  methods: {
    next() {
      if (typeof this.currentChoice === "number") {
        this.choices[this.currentQuestion] = this.currentChoice;
        this.addChoice(this.currentChoice);
        this.currentChoice = null;
        this.page++;
      } else {
        this.showAlert();
      }
    },
    beforeEnd() {
      let allAnswers =
        this.choices.filter(item => typeof item === "number").length + 1;

      if (!(typeof this.currentChoice === "number")) {
        this.showAlert();
      } else {
        if (this.maxPage === allAnswers) {
          this.addChoice(this.currentChoice);
          this.page++;
        } else {
          this.showAlert();
        }
      }
    },
    addChoice(choice) {
      let arr = this.questions,
        index = this.currentQuestion;

      let answer = arr[index].a.find(item => item.id === choice);

      arr[index].choice = answer.points;
    },
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
    },
    showAlert() {
      this.snackbar = true;
    },
    close() {
      this.dialog = false;
    }
  },
  watch: {
    page() {
      this.currentChoice = this.choices[this.currentQuestion];
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

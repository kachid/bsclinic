<template>
  <v-dialog v-model="dialog" persistent max-width="600" content-class="grey lighten-5">
    <template v-slot:activator="{ on }">
      <v-btn class="title" text small color="#1A9EA6" v-on="on">
        Анкета оценки качества сна
      </v-btn>
    </template>
    <v-card class="elevation-6">
      <v-toolbar color="#1A9EA6" dark flat justify="space-between">
        <v-toolbar-title class="title font-weight-medium">
          Субъективная шкала оценки сна Шпигеля
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
          <!-- SLIDERS -->
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="7"
              max="30"
              label="Общая сумма баллов"
              :value="sumAll"
              :rules="rulesForSumAll"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <p>{{ this.resultAll }}</p>
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
        :total-visible="6"
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
    snackbar: false,
    timeout: 2000,
    dialog: false,
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
    currentQuestion() {
      return this.page - 1;
    },
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

      if (points > 22) {
        result = this.results[0];
      } else if (points < 18) {
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

<template>
  <v-dialog v-model="dialog" persistent max-width="600">
    <template v-slot:activator="{ on }">
      <v-btn class="title" text small color="primary" v-on="on">
        Тестирование уровня астении
      </v-btn>
    </template>
    <v-card class="elevation-6">
      <v-toolbar color="primary" dark flat justify="space-between">
        <v-toolbar-title class="title font-weight-medium">
          Субъективная шкала оценки астении
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
              min="0"
              max="100"
              label="Общая сумма баллов"
              :value="sumAll"
              :rules="rulesForSumAll"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <p>{{ this.resultAll }}</p>
          </v-card-text>
          <v-divider></v-divider>
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="20"
              label="Пониженная активность"
              :value="sumA"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <!-- <p>{{ this.resultA }}</p> -->
          </v-card-text>
          <v-divider></v-divider>
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="20"
              label="Физическая астения"
              :value="sumF"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <!-- <p>{{ this.resultF }}</p> -->
          </v-card-text>
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="20"
              label="Общая астения"
              :value="sumG"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <!-- <p>{{ this.resultG }}</p> -->
          </v-card-text>
          <v-divider></v-divider>
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="20"
              label="Снижение мотивации"
              :value="sumM"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <!-- <p>{{ this.resultM }}</p> -->
          </v-card-text>
          <v-divider></v-divider>
          <v-card-text class="my-3 body-1">
            <v-slider
              inverse-label
              readonly
              min="0"
              max="20"
              label="Психическая астения"
              :value="sumP"
              :rules="rules"
              thumb-label="always"
              color="orange darken-3"
            ></v-slider>
            <!-- <p>{{ this.resultP }}</p> -->
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
    snackbar: false,
    timeout: 2000,
    dialog: false,
    rules: [v => v <= 12 || "Астенический синдром"],
    rulesForSumAll: [v => v <= 20 || ""]
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

      if (points < 20) {
        result = this.results[0];
      } else if (points > 30) {
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

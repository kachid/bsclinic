<template>
  <v-dialog v-model="dialog" persistent max-width="600">
    <template v-slot:activator="{ on }">
      <v-btn class="title" text small color="primary" v-on="on">
        Тестирование уровня стресса
      </v-btn>
    </template>
    <v-card class="elevation-6">
      <v-toolbar color="primary" dark flat justify="space-between">
        <v-toolbar-title class="title font-weight-medium">
          Шкала психологического стресса
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
              min="25"
              max="200"
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
    rulesForSumAll: [v => v < 100 || ""]
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
    resultA() {
      return this.countResult(this.sumA, "");
    },
    resultAll() {
      return this.countResult(this.sumAll, "");
    },
    isAlarm() {
      return this.sumAll > 100;
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

      if (points < 100) {
        result = this.results[0];
      } else if (points > 155) {
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
    "никогда",
    "крайне редко",
    "очень редко",
    "редко",
    "иногда",
    "часто",
    "очень часто",
    "постоянно (ежедневно)"
  ],
  answerOptions = [
    { id: 0, answer: answ[0], points: 1 },
    { id: 1, answer: answ[1], points: 2 },
    { id: 2, answer: answ[2], points: 3 },
    { id: 3, answer: answ[3], points: 4 },
    { id: 4, answer: answ[4], points: 5 },
    { id: 5, answer: answ[5], points: 6 },
    { id: 6, answer: answ[6], points: 7 },
    { id: 7, answer: answ[7], points: 8 }
  ],
  que = [
    {
      id: 1,
      type: "A",
      q: "Состояние напряженности и крайней взволнованности (взвинченности)",
      a: answerOptions,
      choice: null
    },
    {
      id: 2,
      type: "A",
      q: "Ощущение кома в горле и/или сухости во рту",
      a: answerOptions,
      choice: null
    },
    {
      id: 3,
      type: "A",
      q: "Я перегружен(а) работой. Мне совсем не хватает времени",
      a: answerOptions,
      choice: null
    },
    {
      id: 4,
      type: "A",
      q: "Я второпях проглатываю пищу или забываю поесть",
      a: answerOptions,
      choice: null
    },
    {
      id: 5,
      type: "A",
      q:
        "После работы я не могу отключиться от мыслей о незавершенных делах, проблемах, планах; я «застреваю» на переживаниях рабочих ситуаций и нерешенных вопросов, обдумываю свои идеи снова и снова",
      a: answerOptions,
      choice: null
    },
    {
      id: 6,
      type: "A",
      q: "Я чувствую себя одиноким(ой) и непонятым(ой)",
      a: answerOptions,
      choice: null
    },
    {
      id: 7,
      type: "A",
      q:
        "Я страдаю от физического недомогания; у меня головокружение, головные боли, напряженность и дискомфорт в области шейного отдела, боли в спине, спазмы в желудке",
      a: answerOptions,
      choice: null
    },
    {
      id: 8,
      type: "A",
      q: "Я поглощен (а) мрачными мыслями, измучен (а) тревожными состояниями",
      a: answerOptions,
      choice: null
    },
    {
      id: 9,
      type: "A",
      q: "Меня внезапно бросает то в жар, то в холод",
      a: answerOptions,
      choice: null
    },
    {
      id: 10,
      type: "A",
      q: "Я забываю о встречах или делах, которые должен сделать или решить",
      a: answerOptions,
      choice: null
    },
    {
      id: 11,
      type: "A",
      q:
        "У меня часто портится настроение; я легко могу заплакать от обиды или проявить агрессию, ярость",
      a: answerOptions,
      choice: null
    },
    {
      id: 12,
      type: "A",
      q: "Я чувствую себя уставшим человеком",
      a: answerOptions,
      choice: null
    },
    {
      id: 13,
      type: "A",
      q: "В трудных ситуациях я крепко стискиваю зубы (или сжимаю кулаки)",
      a: answerOptions,
      choice: null
    },
    {
      id: 14,
      type: "A",
      q: "Я спокоен(на) и безмятежен(на)",
      a: [
        { id: 0, answer: answ[0], points: 8 },
        { id: 1, answer: answ[1], points: 7 },
        { id: 2, answer: answ[2], points: 6 },
        { id: 3, answer: answ[3], points: 5 },
        { id: 4, answer: answ[4], points: 5 },
        { id: 5, answer: answ[5], points: 3 },
        { id: 6, answer: answ[6], points: 2 },
        { id: 7, answer: answ[7], points: 1 }
      ],
      choice: null
    },
    {
      id: 15,
      type: "A",
      q: "Мне тяжело дышать и/или у меня внезапно перехватывает дыхание",
      a: answerOptions,
      choice: null
    },
    {
      id: 16,
      type: "A",
      q:
        "Я имею проблемы с пищеварением и с кишечником (боли, колики, расстройства или запоры)",
      a: answerOptions,
      choice: null
    },
    {
      id: 17,
      type: "A",
      q: "Я взволнован(а), обеспокоен(а), возбужден(а)",
      a: answerOptions,
      choice: null
    },
    {
      id: 18,
      type: "A",
      q: "Я легко пугаюсь; шум или шорох заставляют меня вздрагивать",
      a: answerOptions,
      choice: null
    },
    {
      id: 19,
      type: "A",
      q: "Мне необходимо более чем полчаса для того, чтобы уснуть",
      a: answerOptions,
      choice: null
    },
    {
      id: 20,
      type: "A",
      q:
        "Я сбит(а) с толку; мои мысли спутаны; мне не хватает сосредоточенности и я не могу сконцентрировать внимание",
      a: answerOptions,
      choice: null
    },
    {
      id: 21,
      type: "A",
      q: "У меня усталый вид; мешки или круги под глазами",
      a: answerOptions,
      choice: null
    },
    {
      id: 22,
      type: "A",
      q: "Я чувствую тяжесть на своих плечах",
      a: answerOptions,
      choice: null
    },
    {
      id: 23,
      type: "A",
      q:
        "Я встревожен(а), мне необходимо постоянно двигаться; я не могу стоять или сидеть на одном месте",
      a: answerOptions,
      choice: null
    },
    {
      id: 24,
      type: "A",
      q:
        "Мне трудно контролировать свои поступки, эмоции, настроение или жесты",
      a: answerOptions,
      choice: null
    },
    {
      id: 25,
      type: "A",
      q: "Я чувствую напряженность",
      a: answerOptions,
      choice: null
    }
  ],
  outcome = [
    "Норма, отсутствие достоверно выраженных симптомов",
    "Средний уровень стресса",
    "Высокий уровень стресса, свидетельствует о состоянии дезадаптации и психического дискомфорта, необходимости применения широкого спектра средств и методов для снижения нервно-психической напряженности, психологической разгрузки, изменения стиля мышления и жизни"
  ];
</script>

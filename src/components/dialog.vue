<template>
  <v-col>
    <v-dialog
      v-model="dialog"
      persistent
      max-width="600"
      content-class="grey lighten-5"
    >
      <template v-slot:activator="{ on }">
        <v-btn class="title" text large color="#1A9EA6" v-on="on">
          {{ nameOfTest }}
        </v-btn>
      </template>
      <v-card class="elevation-6">
        <v-toolbar color="#1A9EA6" dark flat justify="space-between">
          <v-toolbar-title class="title font-weight-medium">
            {{ header }}
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
              <p v-if="subquestion" class="font-weight-light subquestion">
                {{ subquestion }}
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
            <div v-for="(slider, i) in sliders" :key="i">
              <v-card-text class="my-3 body-1">
                <v-slider
                  inverse-label
                  readonly
                  :min="slider.min"
                  :max="slider.max"
                  :label="slider.label"
                  :value="slider.value"
                  :rules="slider.rules"
                  thumb-label="always"
                  color="orange darken-3"
                ></v-slider>
                <p v-if="slider.result">{{ slider.result }}</p>
              </v-card-text>
              <v-divider></v-divider>
            </div>
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
          <v-btn v-if="page < maxPage" @click="next" color="#1A9EA6" dark>
            дальше
          </v-btn>
          <v-btn
            v-if="page === maxPage"
            @click="beforeEnd"
            color="#1A9EA6"
            dark
          >
            результат
          </v-btn>
        </v-card-actions>
      </v-card>
      <div v-if="page <= maxPage" class="text-center mt-5">
        <v-pagination
          v-model="page"
          color="#1A9EA6"
          :length="maxPage"
          :total-visible="7"
        ></v-pagination>
      </div>
      <!-- SNACKBAR -->
      <v-snackbar
        class="text-center body-1"
        v-model="snackbar"
        :timeout="2000"
        color="red"
      >
        Нужно выбрать вариант ответа
      </v-snackbar>
    </v-dialog>
  </v-col>
</template>

<script>
export default {
  props: {
    nameOfTest: {
      type: String
    },
    header: {
      type: String
    },
    subquestion: {
      type: String
    },
    isAlarm: {
      type: Boolean
    },
    maxPage: {
      type: Number
    },
    questions: {
      type: Array
    },
    sliders: {
      type: Array
    }
  },
  data: () => ({
    dialog: false,
    choices: [],
    currentChoice: null,
    page: 1,
    snackbar: false
  }),
  computed: {
    currentQuestion() {
      return this.page - 1;
    }
  },
  methods: {
    close() {
      this.dialog = false;
    },
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
          this.$emit("give-qwestions", this.questions);
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
    showAlert() {
      this.snackbar = true;
    }
  },
  watch: {
    page() {
      this.currentChoice = this.choices[this.currentQuestion];
    }
  }
};
</script>

<style scoped>
.subquestion {
  max-width: 360px;
}
</style>

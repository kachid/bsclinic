<template>
  <!-- App.vue -->

  <v-app>
    <v-navigation-drawer app temporary v-model="drawer">
      <v-btn class="ma-3" text icon absolute right @click="drawer = !drawer">
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-btn class="mx-auto mt-10" :href="'/'" large text>
        <v-icon left>mdi-home</v-icon>
        На главную
      </v-btn>
    </v-navigation-drawer>

    <v-app-bar app dark color="primary">
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title
        class="headline text-truncate orange--text darken-3 text-uppercase font-weight-bold"
      >
        Когнитивные тесты для самостоятельной проверки
      </v-toolbar-title>
    </v-app-bar>

    <!-- Sizes your content based upon application components -->
    <v-content>
      <!-- Provides the application the proper gutter -->
      <v-container fluid>
        <v-row align-center justify-center>
          <Card>
            <template v-slot:general>
              <Depression />
            </template>
            <template v-slot:description>
              {{ description.depression }}
            </template>
          </Card>
          <Card>
            <template v-slot:general>
              <Asthenia />
            </template>
            <template v-slot:description>
              {{ description.asthenia }}
            </template>
          </Card>
          <Card>
            <template v-slot:general>
              <Sleep />
            </template>
            <template v-slot:description>
              {{ description.sleep }}
            </template>
          </Card>
          <Card>
            <template v-slot:general>
              <Stress />
            </template>
            <template v-slot:description>
              {{ description.stress }}
            </template>
          </Card>
        </v-row>
      </v-container>
    </v-content>

    <v-footer app>
      <span class="overline"
        >Разработка раздела "Анкеты" &copy;
        <a href="mailto:kachid76@gmail.com">М.Кахидзе</a></span
      >
    </v-footer>
  </v-app>
</template>

<script>
import Card from "@/components/card";
import Depression from "@/components/depression";
import Asthenia from "@/components/asthenia";
import Sleep from "@/components/sleep";
import Stress from "@/components/stress";

export default {
  name: "App",
  components: {
    Card,
    Depression,
    Asthenia,
    Sleep,
    Stress
  },
  data: () => ({
    drawer: false,
    description
  })
};

const description = {
  depression:
    "Госпитальная шкала тревоги и депрессии была разработана для выявления состояний депрессии, тревоги и эмоционального расстройства среди пациентов, которые проходили лечение по разным клиническим заболеваниям.",
  asthenia:
    "В настоящее время у активных работающих людей среднего и даже молодого возраста на фоне напряженного графика, при длительном отсутствии отдыха развивается синдром хронической усталости.",
  sleep:
    "Сон призван обеспечивать оптимальное взаимодействие организма с окружающей средой, подготавливая его к успешной деятельности в период предстоящего бодрствования.",
  stress:
    "Шкала PSM–25 Лемура–Тесье–Филлиона предназначена для измерения феноменологической структуры переживаний стресса. Перевод и адаптация русского варианта методики выполнены Н. Е. Водопьяновой."
};
</script>

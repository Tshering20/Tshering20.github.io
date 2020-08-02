<template>
  <v-app>
    <v-stepper v-model="e1" eager>
      <v-stepper-header>
        <v-stepper-step :complete="e1 > 1" step="1">1980s</v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step :complete="e1 > 2" step="2">1990s</v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step :complete="e1 > 3" step="3">2000s</v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step step="4">2010s</v-stepper-step>
      </v-stepper-header>

      <v-stepper-items>
        <v-stepper-content step="1">
          <v-card  v-if='e1 == 1'
            class="mb-12"
          >
            <Carousel :data='decadeToData["1980s"]'/>
          </v-card>
          <v-btn
            color="primary"
            @click="e1 = 2"
          >
            Next Decade
          </v-btn>

          <v-btn text>Back</v-btn>
        </v-stepper-content>

        <v-stepper-content step="2">
          <v-card v-if='e1 == 2'
            class="mb-12"
          >
            <Carousel :data='decadeToData["1990s"]'/>
          </v-card>

          <v-btn
            color="primary"
            @click="e1 = 3"
          >
            Next Decade
          </v-btn>

          <v-btn text @click="e1 = 1">Back</v-btn>
        </v-stepper-content>

        <v-stepper-content step="3">
          <v-card  v-if='e1 == 3'
            class="mb-12"
          >
            <Carousel :data='decadeToData["2000s"]'/>
          </v-card>

          <v-btn
            color="primary"
            @click="e1 = 4"
          >
              Next Decade
          </v-btn>

          <v-btn text @click="e1 = 2">Back</v-btn>
        </v-stepper-content>

        <v-stepper-content step="4">
          <v-card  v-if='e1 == 4'
            class="mb-12"
          >
            <Carousel :data='decadeToData["2010s"]'/>
          </v-card>

          <v-btn
            color="primary"
            @click="e1 = 1"
          >
              Restart
          </v-btn>
          <v-btn text @click="e1 = 3">Back</v-btn>
        </v-stepper-content>
      </v-stepper-items>
    </v-stepper>
  </v-app>
</template>

<script>
import Carousel from './components/Carousel';
import vg_data from './vgsales.json'

export default {
  name: 'App',

  components: {
    Carousel,
  },
  data: () => ({
    decadeToData: vg_data,
    e1: 1
  }),
  watch: {
    e1: function () {
      this.$forceUpdate()
    }
  }
};
</script>

<style>
  .v-stepper__step__step  {
    display: none !important;
  }
  .v-stepper__step--active {
    font-weight: bold !important;
  }
</style>
<template>
  <v-app>
    <v-app-bar
      dense
      dark
      style='max-height: 50px !important;'
    >
      <img src="./assets/controller.svg" style='height: 40px; margin-right: 20px;'>
      <v-toolbar-title>Video Game Sales Exploration</v-toolbar-title>
    </v-app-bar>
    <Dialog :dialog='dialog' :dialogText='dialogText' @closed='dialog = false'/>
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
            <Carousel :data='decadeToData["1980s"]' :decade="'80s'"/>
          </v-card>
          <v-btn
            color="secondary"
            @click='dialogText = firstTexts; dialog = true'
          >
            Summary
          </v-btn>
          <v-btn
            class='mx-4'
            color="primary"
            @click="e1 = 2"
          >
            Next Decade
          </v-btn>
        </v-stepper-content>

        <v-stepper-content step="2">
          <v-card v-if='e1 == 2'
            class="mb-12"
          >
            <Carousel :data='decadeToData["1990s"]' :decade="'90s'"/>
          </v-card>

          <v-btn
            color="secondary"
            @click='dialogText = secondTexts; dialog = true'
          >
            Summary
          </v-btn>
          <v-btn
            class='mx-4'
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
            <Carousel :data='decadeToData["2000s"]' :decade="'00s'"/>
          </v-card>

          <v-btn
            color="secondary"
            @click='dialogText = thirdTexts; dialog = true'
          >
            Summary
          </v-btn>
          <v-btn
            class='mx-4'
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
            <Carousel :data='decadeToData["2010s"]' :decade="'10s'"/>
          </v-card>

          <v-btn
            color="secondary"
            @click='dialogText = fourthTexts; dialog = true'
          >
            Summary
          </v-btn>
          <v-btn
            class='mx-4'
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
import Dialog from './components/Dialog';
import vg_data from './vgsales.json'

export default {
  name: 'App',

  components: {
    Dialog,
    Carousel,
  },
  data: () => ({
    dialog: true,
    dialogText: ["The dataset used contains a list of video games with sales greater than 100,000 copies obtained from vgchartz.com.", "I have examined a subset of this data to the 100 best selling games according to the decades from the 80s onward"],
    firstTexts: ["The late seventies and early eighties brought the dawn of the home console.", "With the rise of personal computing, mass producing gaming consoles became a reality.", "The creation of the microprocessor led to the successes of the Atari 2600 and then the NES console.", "Nintendo brought the creation of various mascots and the rise of platformers such as Super Mario Bros., The Legend of Zelda, and many more."],
    secondTexts: ["The 90s were marked by innovation and further technological advancements. Raster graphics had transitioned to 3D graphics and gave rise to new genres such as FPS, RTS, and MMO.", "The SNES and Gameboy dominated the first half of the decade with the continuation of their signature characters and mascots.", "The final half of the decade was shaped by the Playstation and Nintendo 64. CD-based games could store massive amounts of data, allowing developers to created complex games such as Final Fantasy VII, Resident Evil, and Metal Gear Solid.", "The 32-bit and 64-bit era had 3D textures and polygons and advances in networking had lead to the creation of online gaming."],
    thirdTexts: ["The 2000s showed further innovation with both consoles and PCs.","In the sixth generation cycle of consoles, Microsoft launched their own console called the Xbox, pushing out companies such as Sega.","Games like Halo gained huge production budgets and revenues and created the AAA industry. Big-budget games such as Call of Duty 4: Modern Warfare and Grand Theft Auto series were released.", "Causal gaming and motion incorpation also gained steam with successes with the Wii and Kinect for Nintendo and Microsoft."],
    fourthTexts: ["Over the last decade, the gaming industry has continued to gain momentum as technology advances and budgeting increases.", "Consoles such as the PS4, Xbox One, and Nintendo Switch have been released recently in the past few years.", "Games have become bigger than ever, the GTA V has made over $6 billion in revenue.", "Both Sony and Microsoft have set their vision on VR gaming and changing the experience of video games"],
    decadeToData: vg_data,
    e1: 1
  }),
  watch: {
    e1: function () {
      this.dialog = false;
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
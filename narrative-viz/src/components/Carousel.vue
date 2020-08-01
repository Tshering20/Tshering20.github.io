<template>
  <v-container>
        <v-carousel 
        hide-delimiter-background
        show-arrows-on-hover>
          <v-carousel-item
            v-for="(decade, i) in chartType"
            :key="i"
            reverse-transition="fade-transition"
            transition="fade-transition"
          >
            <v-sheet
              height="100%"
              color='grey'
            >
              <v-row
                class="fill-height"
                align="center"
                justify="center"
              >
                <div v-for='datum in data' :key='datum.Name'>
                  {{ datum.Name }}
                </div>
              </v-row>
            </v-sheet>
          </v-carousel-item>
        </v-carousel>
  </v-container>
</template>

<script>
// import * as d3 from "d3";

export default {
  name: "Carousel",
  props: ['data'],
  data() {
    return {
      chartType: ['A', 'B', 'C', 'D'],
    };
  },
  mounted() {
 
  },
  methods: {
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

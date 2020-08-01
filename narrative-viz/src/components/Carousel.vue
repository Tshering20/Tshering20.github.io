<template>
  <v-container>
        <v-carousel>
          <v-carousel-item
            v-for="(decade, i) in decades"
            :key="i"
            reverse-transition="fade-transition"
            transition="fade-transition"
          ></v-carousel-item>
        </v-carousel>
  </v-container>
</template>

<script>
// import * as d3 from "d3";
import vg_data from '../vgsales.json'

export default {
  name: "Carousel",
  data() {
    return {
      data: vg_data,
      decades: ['1980s', '1990s', '2000s', '2010s'],
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

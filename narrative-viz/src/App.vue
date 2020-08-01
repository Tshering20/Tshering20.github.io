<template>
  <v-app>
    <Carousel />
  </v-app>
</template>

<script>
import Carousel from './components/Carousel';

export default {
  name: 'App',

  components: {
    Carousel,
  },

  data: () => ({
    //
  }),
};
</script>

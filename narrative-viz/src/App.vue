<template>
  <v-app>
    <v-main>
      <Chart />
    </v-main>
  </v-app>
</template>

<script>
import Chart from './components/Chart';

export default {
  name: 'App',

  components: {
    Chart,
  },

  data: () => ({
    //
  }),
};
</script>

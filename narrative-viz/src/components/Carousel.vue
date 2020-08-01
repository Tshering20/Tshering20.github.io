<template>
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
        >
            <svg></svg>
        </v-sheet>
      </v-carousel-item>
    </v-carousel>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "Carousel",
  props: ['data'],
  data() {
    return {
      chartType: ['A', 'B', 'C', 'D'],
      yearCounts: {},
      genres: {},
      publishers: {},
      platforms: {},
      sales: {'Global_Sales': 0, 'NA_Sales': 0, 'JP_Sales': 0, 'EU_Sales': 0, 'Other_Sales': 0}
    };
  },
  mounted() {
    // Get various summary stats of top 100 best selling games of a decade
    this.data.forEach((datum) => {
      // Get all counts for Genres, Publishers, and Platforms
      if (this.yearCounts[datum.Year]) {
        this.yearCounts[datum.Year] += 1
      } else {
        this.yearCounts[datum.Year] = 1
      }
      if (this.publishers[datum.Publisher]) {
        this.publishers[datum.Publisher] += 1
      } else {
        this.publishers[datum.Publisher] = 1
      }     
      if (this.genres[datum.Genre]) {
        this.genres[datum.Genre] += 1
      } else {
        this.genres[datum.Genre] = 1
      }           
      if (this.platforms[datum.Platform]) {
        this.platforms[datum.Platform] += 1
      } else {
        this.platforms[datum.Platform] = 1
      }      
     // Get total number in sales in millions
      this.sales['Global_Sales'] += datum.Global_Sales
      this.sales['NA_Sales'] += datum.NA_Sales
      this.sales['JP_Sales'] += datum.JP_Sales
      this.sales['EU_Sales'] += datum.EU_Sales
      this.sales['Other_Sales'] += datum.Other_Sales
    });
    this.createSVG();
  },
  methods: {
    createSVG () {
      const margin = 40;
      const width = 1200;
      const height = 400;

      const svg = d3.select('svg');
      const chart = svg.append('g')
      .attr('transform', `translate(${margin}, ${margin})`);
      
      const yScale = d3.scaleLinear()
      .range([height, 0])
      .domain([0, 100]);

      chart.append('g')
      .call(d3.axisLeft(yScale));

      const xScale = d3.scaleBand()
      .range([0, width])
      .domain(Object.keys(this.publishers).map((p) => p))
      .padding(0.2)

      chart.append('g')
      .attr('transform', `translate(0, ${height})`)
      .call(d3.axisBottom(xScale));

      chart.selectAll()
      .data(Object.keys(this.publishers))
      .enter()
      .append('rect')
      .attr('class', 'bar')
      .attr('x', (s) => xScale(s))
      .attr('y', (s) => yScale(this.publishers[s]))
      .attr('height', (s) => height - yScale(this.publishers[s]))
      .attr('width', xScale.bandwidth())
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  svg {
    width: 100%;
    height: 100%;
  }
  .bar {
      fill: #1ca9c9;
  }
</style>

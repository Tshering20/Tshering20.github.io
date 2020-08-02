<template>
    <v-carousel
    v-if='data'
    @change='updateSVG'
    hide-delimiter-background
    show-arrows-on-hover
    v-model='currentChart'>
    <h1 v-if='currentChart != 4' class='chart-title'>TOP {{chartType[currentChart]}} BY COUNT OF THE 100 BEST SELLING GAMES</h1>
    <h1 v-else class='chart-title'>PERCENTAGE OF TOTAL SALES BY REGION FOR 100 BEST SELLING GAMES</h1>
      <v-carousel-item
        eager
        v-for="(type) in chartType"
        :key="type"
      >
        <v-sheet
          width="100%"
          height="100%"
          class="svg-container">
          <svg :id='type'></svg>
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
      currentChart: 'PUBLISHERS',
      chartType: ['PUBLISHERS', 'PLATFORMS', 'GENRES', 'YEARS', 'SALES'],
      yearCounts: {},
      genres: {},
      publishers: {},
      platforms: {},
      sales: {'NA': 0, 'JP': 0, 'EU': 0, 'OT': 0}
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
      // this.sales['Global'] += datum.Global_Sales
      this.sales['NA'] += datum.NA_Sales
      this.sales['JP'] += datum.JP_Sales
      this.sales['EU'] += datum.EU_Sales
      this.sales['OT'] += datum.Other_Sales
    });
    this.updateSVG(0);
  },
  methods: {
    updatePieChart (id, chartData) {
      // use vanilla d3 to create pie chart
      const margin = 40;
      const width = 1200;
      const height = 400;

      let radius = Math.min(width, height) / 2 - margin

      let matching_type = null;
      this.chartType.forEach((type, i) => {
        if (i == id) matching_type = type;
      })

      let svg = d3.select(`svg#${matching_type}`)
      .append("svg")
      .attr("width", width)
      .attr("height", height)
      .append("g")
      .attr("transform", "translate(" + width / 2  + "," + height / 2  + ")");


      let color = d3.scaleOrdinal()
      .domain(chartData)
      .range(d3.schemeSet2);

      let pie = d3.pie()
      .value(function(d) {return d.value; })
      let data_ready = pie(d3.entries(chartData))

      let arcGenerator = d3.arc()
      .innerRadius(0)
      .outerRadius(radius)

      svg
      .selectAll('mySlices')
      .data(data_ready)
      .enter()
      .append('path')
      .attr('d', arcGenerator)
      .attr('fill', function(d){ return(color(d.data.key)) })
      .attr("stroke", "black")
      .style("stroke-width", "2px")
      .style("opacity", 0.7)

      svg
      .selectAll('mySlices')
      .data(data_ready)
      .enter()
      .append('text')
      .text(function(d){ return d.data.key})
      .attr("transform", function(d) { return "translate(" + arcGenerator.centroid(d) + ")";  })
      .style("text-anchor", "middle")
      .style("font-size", 17)
    },
    createSVG (id, chartData) {
      // use vanilla d3 to create bar chart
      const margin = 50;
      const width = 1000;
      const height = 300;

      let matching_type = null;
      this.chartType.forEach((type, i) => {
        if (i == id) matching_type = type;
      })
      let svg = d3.select(`svg#${matching_type}`);
      svg.selectAll("*").remove();
      
      let chart = svg.append('g')
      .attr('transform', `translate(${margin}, ${margin})`);
      
      let maxValue = null;
      if (id == 0) {
        Object.keys(this.publishers).forEach((s) => {
          maxValue = Math.max(maxValue, this.publishers[s])
        })
      } else if (id == 1) {
        Object.keys(this.platforms).forEach((s) => {
          maxValue = Math.max(maxValue, this.platforms[s])
        })
      } else if (id == 2) {
        Object.keys(this.genres).forEach((s) => {
          maxValue = Math.max(maxValue, this.genres[s])
        })
      } else if (id == 3) {
        Object.keys(this.yearCounts).forEach((s) => {
          maxValue = Math.max(maxValue, this.yearCounts[s])
        })
      } else if (id == 4) {
        Object.keys(this.sales).forEach((s) => {
          maxValue = Math.max(maxValue, this.sales[s])
        })
      }

      const yScale = d3.scaleLinear()
      .range([height, 0])
      .domain([0, maxValue]);

      chart.append('g')
      .call(d3.axisLeft(yScale));

      let domain_values = Object.keys(chartData).sort(function(x, y) {
        if (chartData[x] > chartData[y]) {
          return -1;
        }
        if (chartData[x] < chartData[y]) {
          return 1;
        }
        return 0;
      });

      const xScale = d3.scaleBand()
      .range([0, width])
      .domain(domain_values)
      .padding(0.2)

      chart.append("text")
      .attr("class", "y label")
      .attr("text-anchor", "end")
      .attr("y", 6)
      .attr("dy", ".75em")
      .attr("transform", "rotate(-90)")
      .text("life expectancy (years)");

      chart.append('g')
      .attr('transform', `translate(0, ${height})`)
      .call(d3.axisBottom(xScale)).selectAll("text") 
      .style("text-anchor", "end")
      .attr("dx", "1em")
      .attr("dy", "1em")
      .attr("font-weight", "800")
      .attr("transform", "rotate(-14)"); 

      chart.selectAll()
      .data(domain_values)
      .enter()
      .append('rect')
      .attr('class', 'bar')
      .attr('x', (s) => xScale(s))
      .attr('y', (s) => yScale(chartData[s]))
      .attr('height', (s) => height - yScale(chartData[s]))
      .attr('width', xScale.bandwidth());
    },
    updateSVG (slide_num) {      
        if (slide_num == 2) {
        this.createSVG(slide_num, this.genres)
      } else if (slide_num == 1) {
        this.createSVG(slide_num, this.platforms)
      } else if (slide_num == 0) {
        this.createSVG(slide_num, this.publishers)
      } else if (slide_num == 3) {
        this.createSVG(slide_num, this.yearCounts)
      } else {
        this.updatePieChart(slide_num, this.sales)
      }
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
  .chart-title {
    font-size: 20px;
    font-weight: bold; 
    text-align: center;
    margin-bottom: 20px;
  }
  .svg-container{
     display: inline-block;
     margin: 0 auto;
  }
</style>

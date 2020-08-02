<template>
  <div>
      <v-carousel
      v-if='data'
      @change='updateSVG'
      hide-delimiter-background
      show-arrows-on-hover
      v-model='currentChart'>
      <h1 class='chart-title' v-if='currentChart === 0'>100 BEST SELLING GAMES OF THE {{decade}}</h1>
      <h1 v-else-if='currentChart != 5' class='chart-title'>TOP {{chartType[currentChart]}} FOR THE 100 BEST SELLING GAMES</h1>
      <h1 v-else class='chart-title'>PERCENTAGE OF TOTAL SALES BY REGION FOR THE 100 BEST SELLING GAMES</h1>
        <v-carousel-item
          reverse-transition="fade-transition"
          transition="fade-transition"
          eager
          v-for="(type) in chartType"
          :key="type"
        >
          <v-sheet
            width="100%"
            height="100%"
            class="svg-container">
            <v-simple-table v-if='currentChart === 0' height="500px">
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">Rank</th>
                    <th class="text-left">Name</th>
                    <th class="text-left">Total Sales (in millions)</th>
                    <th class="text-left">Platform</th>
                    <th class="text-left">Genre</th>
                    <th class="text-left">Publisher</th>
                    <th class="text-left">Year Released</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(item, i) in data" :key="i">
                    <td>{{ i + 1 }}</td>
                    <td>{{ item.Name }}</td>
                    <td>{{ item.Global_Sales }}</td>
                    <td>{{ item.Platform }}</td>
                    <td>{{ item.Genre }}</td>
                    <td>{{ item.Publisher }}</td>
                    <td>{{ item.Year }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
            <svg :id='type'></svg>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "Carousel",
  props: ['data', 'decade'],
  data() {
    return {
      currentChart: 'TABLE',
      chartType: ['TABLE', 'PUBLISHERS', 'PLATFORMS', 'GENRES', 'YEARS', 'SALES'],
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
    // this.updateSVG(0);
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
      .range(["red","green","blue","yellow"]);

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
      .style("font-weight", "bold")
      .attr("fill", "#fff")

      let total = (chartData["NA"] + chartData["JP"] + chartData["EU"] + chartData["OT"]).toFixed(0)
      svg.append("text").attr("x", 220).attr("y", -30).text(`TOTAL SALES - ${total} million`).style("font-size", "15px").attr("alignment-baseline","middle").attr("fill", "#fff")

      svg.append("circle").attr("cx",200).attr("cy",0).attr("r", 6).style("fill", "red")
      svg.append("circle").attr("cx",200).attr("cy",30).attr("r", 6).style("fill", "green")
      svg.append("circle").attr("cx",200).attr("cy",60).attr("r", 6).style("fill", "blue")
      svg.append("circle").attr("cx",200).attr("cy",90).attr("r", 6).style("fill", "yellow")
      svg.append("text").attr("x", 220).attr("y", 0).text(`North America - ${(100 * (chartData["NA"] / total)).toFixed(2)}%`).style("font-size", "15px").attr("alignment-baseline","middle").attr("fill", "#fff")
      svg.append("text").attr("x", 220).attr("y", 30).text(`Japan - ${(100 * (chartData["JP"] / total)).toFixed(2)}%`).style("font-size", "15px").attr("alignment-baseline","middle").attr("fill", "#fff")
      svg.append("text").attr("x", 220).attr("y", 60).text(`Europe - ${(100 * (chartData["EU"] / total)).toFixed(2)}%`).style("font-size", "15px").attr("alignment-baseline","middle").attr("fill", "#fff")
      svg.append("text").attr("x", 220).attr("y", 90).text(`Others - ${(100 * (chartData["OT"] / total)).toFixed(2)}%`).style("font-size", "15px").attr("alignment-baseline","middle").attr("fill", "#fff")
    },
    createSVG (id, chartData) {
      // use vanilla d3 to create bar chart
      const margin = 80;
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
      if (id == 1) {
        Object.keys(this.publishers).forEach((s) => {
          maxValue = Math.max(maxValue, this.publishers[s])
        })
      } else if (id == 2) {
        Object.keys(this.platforms).forEach((s) => {
          maxValue = Math.max(maxValue, this.platforms[s])
        })
      } else if (id == 3) {
        Object.keys(this.genres).forEach((s) => {
          maxValue = Math.max(maxValue, this.genres[s])
        })
      } else if (id == 4) {
        Object.keys(this.yearCounts).forEach((s) => {
          maxValue = Math.max(maxValue, this.yearCounts[s])
        })
      } else if (id == 5) {
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
      .attr("y", -40)
      .attr("x", -120)
      .attr("transform", "rotate(-90)")
      .attr("fill", "white")
      .text("COUNT");

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
        if (slide_num == 3) {
        this.createSVG(slide_num, this.genres)
      } else if (slide_num == 2) {
        this.createSVG(slide_num, this.platforms)
      } else if (slide_num == 1) {
        this.createSVG(slide_num, this.publishers)
      } else if (slide_num == 4) {
        this.createSVG(slide_num, this.yearCounts)
      } else if (slide_num == 5) {
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

  .arc path {
    stroke: #fff;
  }

  .legend rect {
    fill:white;
    stroke:black;
  }
</style>

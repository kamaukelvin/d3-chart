<template>
  <div id="line-chart" :viewBox="viewBox"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "GraphComponent",
  props: {
    data: {
      required: true,
      type: Array,
    },
    width: {
      default: 500,
      type: Number,
    },
    height: {
      default: 270,
      type: Number,
    },
  },
  data() {
    return {
      padding: 60,
      windowWidth: window.innerWidth,
    };
  },
  methods: {
    renderChart() {
      let margin = { top: 20, right: 20, bottom: 60, left: 50 },
        width,
        height;
      if (this.windowWidth > 800) {
       width = 0.75 * this.windowWidth
      }else{
         width =  this.windowWidth
      }
      width = width - margin.left - margin.right,
        (height = 500 - margin.top - margin.bottom);

      // parse the date / time
      let parseTime = d3.timeParse("%d-%b-%y");

      // set the ranges
      let x = d3.scaleTime().range([0, width]);
      let y = d3.scaleLinear().range([height, 0]);

      let area = d3
        .area()
        .x(function (d) {
          return x(d.date);
        })
        .y0(height)
        .y1(function (d) {
          return y(d.series1);
        })
        .curve(d3.curveMonotoneX);

      let area2 = d3
        .area()
        .x(function (d) {
          return x(d.date);
        })
        .y0(height)
        .y1(function (d) {
          return y(d.series2);
        })
        .curve(d3.curveMonotoneX);

      let svg = d3
        .select("#line-chart")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .attr("fill", "#000")
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      // Get the data
      let data = this.data;

      // format the data
      data.forEach(function (d) {
        d.date = parseTime(d.date);
        d.series1 = +d.series1;
        d.series2 = +d.series2;
      });

      // Scale the range of the data
      x.domain(
        d3.extent(data, function (d) {
          return d.date;
        })
      );
      y.domain([
        0,
        d3.max(data, function (d) {
          return Math.max(d.series1, d.series2);
        }),
      ]);

      svg
        .append("path")
        .data([data])
        .attr("d", area)
        .attr("stroke", " #008ffb")
        .attr("stroke-width", 3)
        .attr("fill", "rgba(209, 235, 254,.6)");
      svg
        .append("path")
        .data([data])
        .attr("d", area2)
        .attr("stroke", "#0de39c")
        .attr("stroke-width", 3)
        .attr("fill", "rgba(168, 235, 230,.4)");

      // Add the X Axis
      svg
        .append("g")

        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x));

      // Add the Y Axis
      svg.append("g").call(d3.axisLeft(y));

   
      // legend
      svg
        .append("circle")
        .attr("cx", 350)
        .attr("cy", 470)
        .attr("r", 6)
        .style("fill", "#69b3a2");
      svg
        .append("circle")
        .attr("cx", 450)
        .attr("cy", 470)
        .attr("r", 6)
        .style("fill", "#404080");
      svg
        .append("text")
        .attr("x", 370)
        .attr("y", 475)
        .text("Series 1")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
      svg
        .append("text")
        .attr("x", 470)
        .attr("y", 475)
        .text("Series 2")
        .style("font-size", "15px")
        .attr("alignment-baseline", "middle");
    },
    onResize() {
      this.windowWidth = window.innerWidth;
    },
  },
  computed: {
    viewBox() {
      return `0 0 ${this.width} ${this.height}`;
    },
  },
  watch: {
    data(val) {
      if (val) {
        this.renderChart();
      }
    },
    windowWidth(newHeight, oldHeight) {
      console.log(`it changed to ${newHeight} from ${oldHeight}`);
    },
  },
  mounted() {
    this.$nextTick(() => {
      window.addEventListener("resize", this.onResize);
    });
    this.renderChart();
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.onResize);
  },
};
</script>

<style scoped>
#line-chart {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>

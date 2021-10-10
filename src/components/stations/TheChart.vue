<template>
  <div class="chart" :id="`${type}-chart`"></div>
</template>

<script>
import d3 from "../../d3-importer.js";

export default {
  props: ["chartData", "type"],
  inject: [
    "focusColor",
    "transitionDuration",
    "mobileWidth",
    "areaOpacity",
    "scrollToId",
  ],
  mounted() {
    // Draw the spider charts
    this.drawD3(this.chartData);
  },
  methods: {
    emitFocusedIndex(index) {
      this.$emit("setFocus", index);
    },
    drawD3(chartData) {
      // Set dimensions and other parameters
      let dimensions = {
        width: 800,
        heigth: 800,
        margins: {
          top: 120,
          right: 120,
          bottom: 120,
          left: 120,
        },
      };

      dimensions.ctrWidth =
        dimensions.width - (dimensions.margins.left + dimensions.margins.right);
      dimensions.ctrHeight =
        dimensions.heigth -
        (dimensions.margins.top + dimensions.margins.bottom);

      const parameters = {
        levels: 3, // Number of inner circles to be drawn
        areaOpacity: this.areaOpacity, // Opacity of the spider area
        fontSize: "12",
      };

      parameters.circleRadius = dimensions.ctrWidth / 2;

      const maxValue = 1;

      // xScale
      const xScale = chartData.areas;

      // yScale
      const circleSlice = (Math.PI * 2) / xScale.length; //The width in radians of each "slice"

      const yScale = d3
        .scaleLinear()
        .range([0, parameters.circleRadius])
        .domain([0, maxValue]);

      // Draw svg image
      const svg = d3
        .select(`#${this.type}-chart`)
        .style("margin-top", 0)
        .append("svg")
        .attr("class", "svg-image")
        .attr("id", `svg-${this.type}`)
        .attr("viewBox", `0 0 ${dimensions.width} ${dimensions.heigth}`);

      // The arc generator
      const radarArc = d3
        .arc()
        .innerRadius(0)
        .outerRadius((_, _2, factor) => yScale(maxValue * factor))
        .startAngle((_, i) => circleSlice * i - circleSlice / 2) // !
        .endAngle((_, i) => circleSlice * (i + 1) - circleSlice / 2); // !

      // The radial line generator
      // Used to draw the data paths/spider areas
      const radarLine = d3
        .lineRadial()
        .curve(d3.curveCardinalClosed)
        .radius((d) => yScale(d))
        .angle((_, i) => i * circleSlice);

      // Add container to svg image (consider margins)
      const ctr = svg
        .append("g")
        .attr(
          "transform",
          `translate(${dimensions.margins.left}, ${dimensions.margins.top})`
        );

      // Add g for the yScale
      const grid = ctr
        .append("g")
        .attr("class", "y-grid")
        .attr(
          "transform",
          `translate(${parameters.circleRadius}, ${dimensions.ctrWidth / 2})`
        );

      const xTicks = grid
        .selectAll(".x-tick")
        .data(xScale)
        .join("g")
        .attr("class", "x-tick");

      // Draw the x-axis tick labels
      xTicks
        .append("text")
        .attr("class", "x-tick-label")
        .style("font-size", parameters.fontSize)
        .each(function(d, i) {
          const centroid = radarArc.centroid(d, i, 2.4);
          d3.select(this)
            .attr("x", centroid[0])
            .attr("y", centroid[1])
            .text((d) => d)
            .attr("fill", "#333447");
        })
        .attr("text-anchor", "middle")
        .lower();

      // yScale circles
      grid
        .selectAll(".y-circle")
        .data(d3.range(1, parameters.levels + 1).reverse())
        .join("circle")
        .attr("class", "y-circle")
        .attr("r", (d) => (parameters.circleRadius / parameters.levels) * d)
        .attr("fill", "none")
        .attr("stroke", "lightgrey");

      const colors = (index) => {
        const colors = ["223949", "8D5F51", "778F9A", "5F6371", "4C687C"]; // PHOTO
        return `#${colors[index % colors.length]}`;
      };

      // Create a wrapper for the areas
      const spiderAreas = grid
        .selectAll(".spider-area")
        .data(chartData[this.type])
        .join("g")
        .attr("class", "spider-area")
        .attr("opacity", parameters.areaOpacity);

      // 'this' is going to be occupied
      const thisType = this.type;
      // const thisFocusColor = this.focusColor;
      const thisDuration = this.transitionDuration;
      const thisMobileWidth = this.mobileWidth;
      const getMarginTop = (type, index) => {
        const item = document.getElementById(`${type}-${index}`);
        const chart = document.getElementById(`${type}-chart`);
        return chart.offsetHeight / 2 - item.offsetHeight / 2;
      };
      const thisScrollToId = this.scrollToId;
      const thisEmitFocusedIndex = this.emitFocusedIndex;

      // Create the spider areas
      spiderAreas
        .append("path")
        .attr("class", `${this.type}-path`)
        .attr("id", (_, i) => `${this.type}Area-${i}`)
        .attr("d", (d) => {
          return radarLine(d.areaValues);
        })
        .attr("stroke", "none")
        .attr("fill", (_, i) => colors(i))
        .attr("fill-opacity", parameters.areaOpacity)
        .on("mouseover", function() {
          if (window.innerWidth >= thisMobileWidth) {
            // "Hide" all spider areas
            d3.selectAll(`.${thisType}-path`).attr("fill-opacity", "0.1");
            // Highlight current area
            d3.select(this).attr("fill-opacity", "1");
            // Get index of current area
            const index = d3
              .select(this)
              .attr("id")
              .split("-")[1];
            // Hide all item descriptions
            d3.selectAll(`.${thisType}`).style("display", "none");

            // Emit focused index
            thisEmitFocusedIndex(parseInt(index));

            // Show and position currently corresponding item description next to spider chart
            d3.select(`#${thisType}-${index}`)
              .style("display", "block")
              .style("margin-top", () => {
                if (window.innerWidth >= thisMobileWidth) {
                  return `${getMarginTop(thisType, index)}px`;
                } else {
                  return "0px";
                }
              });
            // Scroll to corresponding section
            thisScrollToId(`#sec-${thisType}`);
          }
        })
        .on("mouseout", function() {
          if (window.innerWidth >= thisMobileWidth) {
            // Show all spider areas
            d3.selectAll(`.${thisType}-path`).attr(
              "fill-opacity",
              parameters.areaOpacity
            );
            // Get index of current area
            const index = d3
              .select(this)
              .attr("id")
              .split("-")[1];
            // Show all item descriptions
            d3.selectAll(`.${thisType}`).style("display", "block");

            // Emit focused index
            thisEmitFocusedIndex(null);

            // Set back margin-top
            d3.select(`#${thisType}-${index}`)
              .transition()
              .duration(thisDuration)
              .style("margin-top", "0px");
          }
        })
        .on("touchstart", function() {
          d3.select(`#${thisType}-chart`).style("margin-top", "0px");
          // Check if already selected
          const hasFocus = d3.select(this).attr("fill-opacity") === "1";

          // Get index of current area
          const index = d3
            .select(this)
            .attr("id")
            .split("-")[1];

          if (hasFocus) {
            // When selected
            // Show all spider areas
            d3.selectAll(`.${thisType}-path`).attr(
              "fill-opacity",
              parameters.areaOpacity
            );
            // Show all item descriptions
            d3.selectAll(`.${thisType}`)
              .style("display", "block")
              .style("margin-top", "0px");

            // Show all item descriptions
            d3.select("#job-chart")
              .style("margin-top", "0px");

            // Emit focused index
            thisEmitFocusedIndex(null);
          } else {
            // When not selected
            // "Hide" all spider areas
            d3.selectAll(`.${thisType}-path`).attr("fill-opacity", "0.1");
            // Highlight current area
            d3.select(this).attr("fill-opacity", "1");

            // Emit focused index
            thisEmitFocusedIndex(parseInt(index));

            // Hide all item descriptions
            d3.selectAll(`.${thisType}`).style("display", "none");
            // Show only currently selected item description
            d3.select(`#${thisType}-${index}`)
              .style("display", "block")
              .style("margin-top", () => {
                if (window.innerWidth < thisMobileWidth) {
                  return "0px";
                } else {
                  return `${getMarginTop(thisType, index)}px`;
                }
              });
          }
        });
    },
  },
};
</script>

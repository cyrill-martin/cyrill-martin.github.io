<template>
  <div
    class="life-station job"
    :style="{ 'background-color': hasFocus ? focusColor : 'white' }"
    :id="`job-${index}`"
    @mouseover="highlightJob"
    @mouseout="highlightJob"
    @touchstart="highlightJobTablet"
    @click="highlightJobMobile"
  >
    <div class="job-title">{{ job.title }}</div>
    <div class="job-company">
      <a :href="job.url" target="_blank">{{ job.company }}</a> · {{ job.type }}
    </div>
    <div class="job-period">{{ job.period.from }} – {{ job.period.to }}</div>
    <div class="job-summary" v-html="job.summary"></div>
    <div v-if="job.stack.length" class="job-stack">
      {{ job.stack.join(" · ") }}
    </div>
  </div>
</template>

<script>
import d3 from "../../d3-importer.js";

export default {
  emits: ["setFocus"],
  props: ["job", "index", "focusedIndex"],
  inject: [
    "focusColor",
    "numberOfJobs",
    "transitionDuration",
    "mobileWidth",
    "areaOpacity",
    "scrollToId",
  ],
  created() {
    window.addEventListener("resize", this.clearFocus);
  },
  data() {
    return {
      hasFocus: false,
      marginTop: null,
    };
  },
  watch: {
    focusedIndex(value) {
      if (value === this.index) {
        this.hasFocus = true;
      } else {
        this.hasFocus = false;
      }
    },
  },
  methods: {
    clearFocus() {
      this.hasFocus = false;
    },
    changeJobFocus() {
      this.hasFocus = !this.hasFocus;
      if (this.hasFocus) {
        this.$emit("setFocus", this.index);
      } else {
        this.$emit("setFocus", null);
      }
    },
    changeAreaFocus() {
      if (this.hasFocus) {
        d3.selectAll(".job-path").attr("fill-opacity", "0.1");
        d3.select(`#jobArea-${this.index}`).attr("fill-opacity", "1");
      } else {
        d3.selectAll(".job-path").attr("fill-opacity", this.areaOpacity);
      }
    },
    getMarginTop() {
      const job = document.getElementById(`job-${this.index}`);
      const chart = document.getElementById("job-chart");
      const newChartPos =
        job.offsetTop - (chart.offsetHeight / 2 - job.offsetHeight / 2);
      if (newChartPos > 0) {
        return newChartPos;
      } else {
        return 0;
      }
    },
    setChartMargin() {
      d3.select(`#job-chart`)
        .transition()
        .duration(this.transitionDuration)
        .style("margin-top", `${this.getMarginTop()}px`);
    },
    highlightJob() {
      if (window.innerWidth >= this.mobileWidth) {
        this.changeJobFocus();
        this.changeAreaFocus();
        this.setChartMargin();
      }
    },
    highlightJobTablet() {
      if (window.innerWidth >= this.mobileWidth) {
        this.changeJobFocus();
        this.changeAreaFocus();
        if (this.hasFocus) {
          this.setChartMargin();
        } else {
          // Make sure all item descriptions are shown
          d3.selectAll(".job")
            .style("display", "block")
            .style("margin-top", "0px");
          d3.select("#job-chart")
            // .transition()
            // .duration(this.transitionDuration)
            .style("margin-top", "0px");
        }
      }
    },
    highlightJobMobile() {
      if (window.innerWidth < this.mobileWidth) {
        // Scroll to corresponding section
        this.scrollToId("#job-chart");
        // Set focus
        this.changeJobFocus();
        // Set spider area
        this.changeAreaFocus();
        if (this.hasFocus) {
          // Hide all item descriptions
          d3.selectAll(".job").style("display", "none");
          // Show only currently selected item description
          d3.select(`#job-${this.index}`).style("display", "block");
        } else {
          // Show all item descriptions
          d3.selectAll(".job")
            .style("display", "block")
            .style("margin-top", "0px");
          this.scrollToId(`#job-${this.index}`);
          window.scrollBy(
            0,
            -(parseInt(document.getElementById("job-chart").offsetHeight) + 10)
          );
        }
      }
    },
  },
};
</script>

<style scoped>
.job-title {
  font-size: 1.1rem;
  font-weight: 700;
}

.job-company {
  font-weight: 700;
}

.job-period {
  color: grey;
}

.job-summary {
  margin: 1rem 0;
}

@media only screen and (min-width: 45em) {
  .job-title {
    font-size: 1.2rem;
  }
}
</style>

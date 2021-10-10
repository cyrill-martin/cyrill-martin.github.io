<template>
  <div class="row">
    <div class="col-12">
      <h2>Experience</h2>
    </div>
  </div>
  <div class="row" @mouseout="resetChartMargin">
    <div class="col-5">
      <the-chart
        :chart-data="experience"
        :type="'job'"
        @set-focus="setFocus"
      ></the-chart>
    </div>
    <div class="col-7">
      <the-job
        v-for="(job, index) in experience.job"
        :key="job.title"
        :job="job"
        :index="index"
        :focusedIndex="focusedIndex"
        @set-focus="setFocus"
      ></the-job>
    </div>
  </div>
</template>

<script>
import d3 from "../../d3-importer.js";
import TheJob from "./TheJob.vue";
import TheChart from "./TheChart.vue";

export default {
  components: {
    TheJob,
    TheChart,
  },
  props: ["experience"],
  inject: ["transitionDuration"],
  data() {
    return {
      focusedIndex: null,
    };
  },
  methods: {
    setFocus(index) {
      this.focusedIndex = index;
    },
    resetChartMargin() {
      d3.select(`#job-chart`)
        .transition()
        .delay(this.transitionDuration)
        .duration(this.transitionDuration)
        .style("margin-top", "0px");
    },
  },
};
</script>

<style>
.chart-tooltip {
  padding: 5px;
  display: none;
}
</style>

<template>
  <div class="row">
    <div class="col-12">
      <h2>Education</h2>
    </div>
  </div>
  <div class="row">
    <div class="col-5">
      <the-chart
        :chart-data="education"
        :type="'education'"
        @set-focus="setFocus"
      ></the-chart>
    </div>
    <div class="col-7">
      <the-degree
        v-for="(degree, index) in education.education"
        :key="index"
        :degree="degree"
        :index="index"
        :focusedIndex="focusedIndex"
        @set-focus="setFocus"
      ></the-degree>
    </div>
  </div>
</template>

<script>
import TheDegree from "./TheDegree.vue";
import TheChart from "./TheChart.vue";

export default {
  components: {
    TheDegree,
    TheChart,
  },
  props: ["education"],
  data() {
    return {
      focusedIndex: null,
    };
  },
  methods: {
    setFocus(index) {
      this.focusedIndex = index;
    },
  },
};
</script>

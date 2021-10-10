<template>
  <div
    class="life-station education"
    :style="{ 'background-color': hasFocus ? focusColor : 'white' }"
    :id="`education-${index}`"
    @mouseover="highlightEdu"
    @mouseout="highlightEdu"
    @touchstart="highlightEduTablet"
    @click="highlightEduMobile"
  >
    <div class="degree-institution">{{ degree.institution }}</div>
    <div class="degree-degree">{{ degree.degree }}</div>
    <div class="degree-period">
      {{ degree.period.from }} – {{ degree.period.to }}
    </div>
    <div
      v-if="degree.summary"
      class="degree-summary"
      v-html="degree.summary"
    ></div>
  </div>
</template>

<script>
import d3 from "../../d3-importer.js";

export default {
  emits: ["setFocus"],
  props: ["degree", "index", "focusedIndex"],
  inject: [
    "focusColor",
    "mobileWidth",
    "areaOpacity",
    "transitionDuration",
    "scrollToId",
  ],
  created() {
    window.addEventListener("resize", this.clearFocus);
  },
  data() {
    return {
      hasFocus: false,
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
    changeEduFocus() {
      this.hasFocus = !this.hasFocus;
      if (this.hasFocus) {
        this.$emit("setFocus", this.index);
      } else {
        this.$emit("setFocus", null);
      }
    },
    changeAreaFocus() {
      if (this.hasFocus) {
        d3.selectAll(".education-path").attr("fill-opacity", "0.1");
        d3.select(`#educationArea-${this.index}`).attr("fill-opacity", "1");
      } else {
        d3.selectAll(".education-path").attr("fill-opacity", this.areaOpacity);
      }
    },
    highlightEdu() {
      if (window.innerWidth >= this.mobileWidth) {
        this.changeEduFocus();
        this.changeAreaFocus();
      }
    },
    highlightEduTablet() {
      if (window.innerWidth >= this.mobileWidth) {
        this.changeEduFocus();
        this.changeAreaFocus();
        if (!this.hasFocus) {
          // Make sure all item descriptions are shown
          d3.selectAll(".education")
            .style("display", "block")
            .style("margin-top", "0px");
          d3.select("#education-chart")
            // .transition()
            // .duration(this.transitionDuration)
            .style("margin-top", "0px");
        }
      }
    },
    highlightEduMobile() {
      if (window.innerWidth < this.mobileWidth) {
        // Scroll to corresponding section
        this.scrollToId("#education-chart");
        this.changeEduFocus();
        this.changeAreaFocus();
        if (this.hasFocus) {
          // Hide all item descriptions
          d3.selectAll(`.education`).style("display", "none");
          // Show only currently selected item description
          d3.select(`#education-${this.index}`).style("display", "block");
        } else {
          // Show all item descriptions
          d3.selectAll(".education").style("display", "block");
          // Scroll to corresponding section
          this.scrollToId(`#education-${this.index}`);
          window.scrollBy(
            0,
            -(
              parseInt(
                document.getElementById("education-chart").offsetHeight
              ) + 10
            )
          );
        }
      }
    },
  },
};
</script>

<style scoped>
.degree-institution {
  font-size: 1.2rem;
  font-weight: 700;
}

.degree-degree {
  font-weight: 700;
}

.degree-period {
  color: grey;
}

.degree-summary {
  margin: 1rem 0;
}
</style>

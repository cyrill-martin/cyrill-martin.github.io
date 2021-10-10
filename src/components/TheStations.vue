<template>
  <section id="sec-job">
    <the-experience :experience="experience"></the-experience>
  </section>

  <section id="sec-education">
    <the-education :education="education"></the-education>
  </section>
</template>

<script>
import TheExperience from "./stations/TheExperience.vue";
import TheEducation from "./stations/TheEducation.vue";

export default {
  components: {
    TheExperience,
    TheEducation,
  },
  props: ["experience", "education"],
};
</script>

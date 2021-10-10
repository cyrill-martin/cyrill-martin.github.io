<template>
  <div class="container page">
    <header class="row">
      <div class="col-12"></div>
    </header>

    <main class="row">
      <section>
        <the-personal-details
          :full-name="fullName"
          :image="cv.image"
          :job-title="cv.headline.jobTitle"
          :summary="cv.headline.summary"
          :contact="cv.contact"
          :social-network="cv.socialNetwork"
        ></the-personal-details>
      </section>

      <section id="sec-job">
        <the-experience :experience="cv.experience"></the-experience>
      </section>

      <section id="sec-education">
        <the-education :education="cv.education"></the-education>
      </section>

      <section id="sec-digital">
        <the-digital :digital="cv.digital"></the-digital>
      </section>

      <section id="sec-languages">
        <the-languages :languages="cv.languages"></the-languages>
      </section>

      <section id="sec-publications">
        <the-publications :publications="cv.publications"></the-publications>
      </section>

      <section id="sec-interests">
        <the-interests :interests="cv.interests"></the-interests>
      </section>
    </main>

    <footer class="row">
      <div class="col-12">
        &#169; 2021 Cyrill Martin - Vue.js files available
        <a href="https://github.com/cyrill-martin/cyrill-martin.github.io" target="_blank"
          >here</a
        >
      </div>
    </footer>
  </div>
</template>

<script>
import ThePersonalDetails from "./components/ThePersonalDetails.vue";
import TheExperience from "./components/stations/TheExperience.vue";
import TheEducation from "./components/stations/TheEducation.vue";
import TheDigital from "./components/TheDigital.vue";
import TheLanguages from "./components/TheLanguages.vue";
import ThePublications from "./components/ThePublications.vue";
import TheInterests from "./components/TheInterests.vue";
import d3 from "./d3-importer.js";
import myCv from "./myCv.json"

export default {
  components: {
    ThePersonalDetails,
    TheExperience,
    TheEducation,
    TheDigital,
    TheLanguages,
    ThePublications,
    TheInterests,
  },
  created() {
    window.addEventListener("resize", this.resetSelections);
  },
  computed: {
    fullName() {
      if (this.cv.middleName) {
        return `${this.cv.firstName} ${this.cv.middleName} ${this.cv.lastName}`;
      } else {
        return `${this.cv.firstName} ${this.cv.lastName}`;
      }
    },
    numberOfJobs() {
      return this.cv.experience.job.length;
    },
  },
  provide() {
    return {
      focusColor: this.focusColor,
      numberOfJobs: this.numberOfJobs,
      transitionDuration: this.transitionDuration,
      mobileWidth: this.mobileWidth, // Corresponding to global CSS
      areaOpacity: this.areaOpacity,
      scrollToId: this.scrollToId,
    };
  },
  methods: {
    scrollToId(id) {
      location.href = id;
      // Clean up URL
      window.history.replaceState(
        { additionalInformation: "Updated the URL with JS" },
        "Cyrill Martin | Curriculum Vitae",
        this.url
      );
    },
    resetSelections() {
      // Show all item descriptions
      d3.selectAll(".job").style("display", "block");
      d3.selectAll(".job-path").attr("fill-opacity", this.areaOpacity);
      d3.selectAll(".education").style("display", "block");
      d3.selectAll(".education-path").attr("fill-opacity", this.areaOpacity);
      d3.selectAll(".chart").style("margin-top", "0px");
    },
  },
  data() {
    return {
      url: process.env.VUE_APP_URL,
      focusColor: "rgb(245, 245, 245)",
      transitionDuration: 250,
      mobileWidth: 720, // Corresponding to global CSS
      areaOpacity: "0.65",
      cv: myCv,
    };
  },
};
</script>

<style scoped>
.page {
  position: relative;
  min-height: 100vh;
  padding: 0 1.5rem;
}

header {
  height: 4rem;
}

footer {
  margin-top: 2rem;
  height: 4rem;
}

@media only screen and (max-width: 45em) {
  header {
    height: 0;
  }

  .page {
    padding: 0;
  }
}
</style>

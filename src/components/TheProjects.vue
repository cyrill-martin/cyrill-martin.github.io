<template>
  <div class="row">
    <div class="col-12">
      <h2>Open Projects</h2>
    </div>
  </div>
  <div class="row">
    <table class="col-12">
      <tr v-for="(project, index) in projects" :key="index">
        <td class="label">
          <a v-if="project.url" :href="project.url" target="_blank">{{
            project.title
          }}</a>
          <span v-else>{{
            project.title
          }}</span>
        </td>
        <td class="project-link">
          <a v-if="project.gitHub" :href="project.gitHub" target="_blank">GitHub</a>
          <span v-else> </span>
        </td>
        <td class="project-summary">
          <span v-html="project.summary"></span>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  props: ["projects"],
};
</script>

<style scoped>
.project-link {
  padding-right: 1rem;
}
</style>

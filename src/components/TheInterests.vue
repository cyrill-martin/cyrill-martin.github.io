<template>
  <div class="row">
    <div class="col-12">
      <h2>Interests</h2>
    </div>
  </div>
  <div class="row">
    <div class="col-12">
      <span v-for="(int, index) in interests" :key="index" class="interest">
        <a v-if="int.url" :href="int.url" target="_blank">{{ int.topic }}</a>
        <span v-else>{{ int.topic }}</span>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  props: ["interests"],
};
</script>

<style scoped>
.interest + .interest::before {
  content: " · ";
}
</style>

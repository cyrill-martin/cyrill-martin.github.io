<template>
  <div class="row">
    <div class="col-12">
      <h2>Publications</h2>
    </div>
  </div>
  <div class="row">
    <div class="col-12">
      <div v-for="(pub, index) in publications" :key="index">
        <span v-html="pub"></span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["publications"],
};
</script>

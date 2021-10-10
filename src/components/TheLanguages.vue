<template>
  <div class="row">
    <div class="col-12">
      <h2>Languages</h2>
    </div>
  </div>
  <div class="row">
    <table class="col-12">
      <tr v-for="(lang, index) in languages" :key="index">
        <td class="label">{{ lang.lang }}</td>
        <td>{{ lang.level }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  props: ["languages"],
};
</script>

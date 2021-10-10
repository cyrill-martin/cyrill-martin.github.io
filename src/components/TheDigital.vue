<template>
  <div class="row">
    <div class="col-12">
      <h2>Digital</h2>
    </div>
  </div>
  <div class="row">
    <table class="col-12">
      <tr v-for="(skill, index) in digital" :key="index">
        <td class="label">{{ skill.skill }}</td>
        <td>{{ skill.stack.join(" · ") }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  props: ["digital"],
};
</script>
